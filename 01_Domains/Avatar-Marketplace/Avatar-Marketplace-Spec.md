---
title: Avatar Marketplace — đặc tả MVP tinh gọn
date: 2026-11-07
status: business-approved-implementation-pending
tags:
  - domain
  - avatar-marketplace
  - specification
aliases:
  - Avatar Marketplace Spec
  - Marketplace Avatar
---

# Avatar Marketplace — đặc tả MVP đã chốt

Ngày: 2026-11-07. **Product owner đã duyệt phạm vi, yêu cầu sync Brain và trình task plan trước Jira. Implementation chưa thực hiện.**

> [!important] Nguồn chuẩn
> Đây là business/spec đã đồng ý cho MVP, không phải tuyên bố tính năng có trên application main. Target DB/API/index/lock contract cần technical review khi triển khai. Decision 26 thay phạm vi loại Marketplace cũ của Decision 10. Task execution thuộc repo ứng dụng/Jira sau duyệt, không đưa tiến độ sprint vào Brain.

## 1. Quyết định đã rõ

- Dùng hướng bán nhiều bản quyền sử dụng: seller giữ avatar gốc, buyer nhận avatar riêng trong own inventory, cùng asset. Product owner đã chấp thuận hướng này và phạm vi tối giản bên dưới.
- Thêm avatar_assets để quản lý file chung độc lập với inventory; bỏ preview_path và source khỏi target schema.
- Thêm avatar_listings cho tin đăng, giá và moderation.
- Không tạo avatar_purchases; không thêm purpose, access_status hoặc avatar_listing_id vào transactions. Ghi nội dung giao dịch vào description theo yêu cầu user.
- Nam dùng avatar đã cấp trong own inventory bình thường, không query transaction hoặc trạng thái quyền dùng riêng mỗi lần render/start.
- Không dùng parse/search description để tự suy ra entitlement, chống mua trùng mọi thời đại, thống kê theo listing hoặc khôi phục miễn phí sau khi avatar đã bị xóa.

## 2. Phạm vi sản phẩm đã chốt

| Phạm vi | MVP |
|---|---|
| Buyer/seller | Candidate và Recruiter hợp lệ; Admin chỉ duyệt/quản lý |
| Thanh toán | Coins nội bộ; nạp coins qua flow PayOS hiện tại nếu thiếu |
| Seller nhận | Coins trong ví; chưa cash-out |
| Phí sàn | Không có phí sàn trong MVP; seller nhận toàn bộ giá mua bằng coins. Thêm phí sau này phải chốt business/hạch toán riêng |
| Tin đăng | Seller tạo/sửa draft hoặc rejected, submit; Admin approve/reject; seller ngừng bán; Admin ẩn tin vi phạm |
| Xem sản phẩm | Danh sách, tìm kiếm cơ bản, chi tiết và preview ảnh hoặc ảnh mặc định; không cấp production file trước mua |
| Sau mua | Buyer nhận avatar row trong inventory và chọn dùng cho practice/own Job Posting theo vai trò như avatar hiện có |
| Slot | Mỗi avatar mua chiếm 1 slot; hết slot xử lý bằng flow mua capacity/xóa avatar hiện có trước checkout |
| Lịch sử | Dùng lịch sử transactions hiện có với description dễ đọc, không xây dashboard doanh số từng sản phẩm |
| Seller ngừng bán/xóa bản gốc | Không xóa avatar buyer đã mua hoặc file asset còn được tham chiếu |
| Bán lại/xuất ngoài app | Chưa trong MVP; chỉ creator đủ quyền được tạo listing, bản mua không trở thành sản phẩm để buyer đăng lại |
| Xóa bản mua | Trả slot theo flow xóa avatar hiện có; không kèm cam kết restore miễn phí từ lịch sử text. Muốn có lại phải mua lại nếu còn bán, trừ khi product owner chọn flow giữ liên kết sở hữu riêng sau này |

Điểm phân biệt: chống retry charge lần hai vẫn bắt buộc; không đồng nhất nó với quyền khôi phục/không mua lại vĩnh viễn sau khi user tự xóa avatar.

## 3. Flow nghiệp vụ

### Seller

Inventory → chọn avatar mình tạo và được phép phân phối → tạo tin (tên/mô tả/personality mặc định/giá) → lưu nháp hoặc gửi duyệt → pending → Admin approve thành active hoặc reject có lý do → hiển thị Marketplace. Seller xem danh sách tin của mình, sửa/resubmit draft/rejected hoặc ngừng bán tin active.

MVP không sửa model/nội dung tin active trực tiếp; thay đổi nội dung cần quy trình duyệt lại, không đổi model của bản buyer đã nhận. Seller/asset của tin phải ổn định. Relist/đổi giá nếu cần sẽ được ghi contract rõ trước triển khai, không để sửa payload sau Admin approve ngầm.

### Buyer

Marketplace → browse/search → chi tiết/preview/giá/seller → Mua → kiểm tra tài khoản/coins/slot/listing còn active/giá xác nhận → trừ buyer/cộng seller + ghi transaction + cấp avatar trong cùng DB transaction → avatar có trong inventory → dùng trong practice/own posting.

- Self-purchase bị từ chối. Nếu buyer đang có avatar cùng asset trong inventory thì hiển thị Đã có và không charge thêm; kiểm tra dưới inventory lock. Sau khi xóa row này, không còn cam kết giữ sở hữu vĩnh viễn chỉ từ description.
- Không charge trước khi đủ điều kiện; cùng idempotency key không tạo debit/grant thứ hai. Existing unique(from,idempotency_key) là backstop nhưng cần service lock/validation.
- Same-key response sau timeout không tự regrant avatar đã bị user xóa. Replay/intent validation phải review bằng dữ liệu giao dịch và state được lưu hiện có; không hứa listing-level FK hoặc hoàn chỉnh payload matching khi chỉ có description.
- Giá thay đổi so với xác nhận phải báo lại, không charge giá khác ngầm. Không mua slot tự động hoặc trộn phí slot vào giá avatar.

### Admin

Danh sách pending → chi tiết/preview và thông tin nguồn/quyền bán → approve/reject có lý do. Tin vi phạm đang active có thể bị suspended/ẩn khỏi giao dịch mới. Việc gỡ tin không tự thu hồi avatar của các buyer trước đó.

## 4. Use cases trong bản MVP này

10 capability/usecase names dưới đây được duyệt cho MVP; không tự cấp/renumber formal UC IDs/count của tài liệu Capstone.

### Candidate/Recruiter — 7 use cases Marketplace mới

1. **Xem/tìm kiếm Marketplace:** danh sách sản phẩm đang active, tìm kiếm cơ bản.
2. **Xem chi tiết avatar đang bán:** seller, giá, mô tả/personality, preview và trạng thái đã có trong inventory.
3. **Xem các tin đăng của tôi:** own listings theo trạng thái, lý do bị từ chối nếu có.
4. **Tạo và gửi duyệt tin bán avatar:** chọn avatar đủ điều kiện, nhập thông tin, lưu nháp/submit.
5. **Sửa và gửi duyệt lại tin bán:** draft/rejected; không đổi payload active đã duyệt ngầm.
6. **Ngừng bán avatar:** delist own active listing; không ảnh hưởng các avatar đã cấp cho buyer.
7. **Mua avatar bằng coins:** kiểm tra giá/slot/wallet, settlement atomic, ghi transaction description và avatar vào inventory, retry không charge lại.

### Admin — 3 use cases Marketplace mới

8. **Xem danh sách tin chờ duyệt:** queue theo trạng thái, xem tin cần xử lý.
9. **Duyệt/từ chối tin bán:** review đúng payload, ghi người/thời điểm/lý do duyệt.
10. **Ẩn/đình chỉ tin vi phạm:** chặn giao dịch mới, giữ dữ liệu/avatar đã cấp; xử lý thu hồi riêng không tự phát sinh.

### Mở rộng use case hiện có, không tạo thêm chức năng trùng

- **Quản lý Inventory:** hiển thị avatar đã mua như avatar của user; giữ slot và in-use delete restrictions.
- **Cấu hình Interview/Job Posting:** chọn được avatar mua trong own inventory như avatar tự tạo, vẫn theo permissions của vai trò.
- **Xem lịch sử giao dịch/ví:** đọc from/to/amount/currency/status/description; không thêm trang purchase entitlement hoặc báo cáo theo listing.
- **Tạo/xóa avatar và account erasure:** tạo asset độc lập, cleanup có reference check, không xóa file buyer khác còn dùng.

## 5. Phạm vi DB

### Target schema: đúng hai bảng mới

- avatar_assets: một model/file dùng chung, đã được user đồng ý về hướng thiết kế.
- avatar_listings: tin bán/giá/moderation. Fields bên dưới là target DDL để review constraint/index/precision khi implementation, không áp schema trong lượt spec này.

### Bảng avatars hiện có

- Thêm asset_id UUID NOT NULL FK avatar_assets.
- Chuyển model_path sang avatar_assets, sau đó bỏ field cũ theo migration strategy. Avatar tự tạo và avatar mua cùng dùng asset reference.
- Không thêm purchase_transaction_id trong bản tối giản này vì chưa có use case restore/entitlement có cấu trúc cần giữ; việc cấp avatar thuộc inventory là kết quả DB atomic của checkout.
- Giữ id, inventory_id, name, personality, created_at, updated_at. Buyer có avatar ID/inventory riêng, model chung qua asset_id.

### transactions, wallets, inventories

- Không thêm cột Marketplace vào transactions và không tạo avatar_purchase/purchase/entitlement table.
- transactions dùng from/to/amount/currency/status/description/created_at/idempotency_key hiện có. Ví dụ description: "Nam mua avatar Alex từ Bảo"; các ID/tên cần hiển thị có thể ghi text nhưng không dùng parser text để giả một FK/registry quyền sở hữu.
- Tiền ghi amount thực trả; lịch sử không đọc lại giá hiện tại của listing. Seller/buyer là from/to; seller earnings không được tính nhầm thành PayOS top-up hoặc doanh thu tiền thật.
- wallets vẫn balance hiện có; inventories vẫn slot/capacity hiện có. Code phải hỗ trợ chuyển coins user→user trong cùng transaction, vì helper ledger hiện tại chỉ user↔system.

### 5.1. avatar_assets — model/file dùng chung

Đề xuất chuẩn hóa asset cho cả avatar tự tạo và bản mua: tạo avatar vẫn dùng pipeline cũ nhưng kết quả file được ghi vào avatar_assets; avatars tham chiếu asset_id. Một file có thể được nhiều avatar inventory sử dụng. Asset payload bất biến sau khi tạo; model mới là asset ID mới.

| Field | Kiểu đề xuất | Mục đích |
|---|---|---|
| id | UUID PK | ID của model/file, ví dụ ASSET-A |
| created_by | UUID nullable FK users | User tạo/đưa asset vào hệ thống; không phải danh sách mọi người có quyền dùng. Có thể anonymize khi erase theo policy |
| model_path | TEXT NOT NULL | Storage key private của VRM; chuyển từ avatars.model_path sang đây, không lưu signed URL hết hạn |
| status | enum/check TEXT | ready hoặc blocked; blocked ngăn cấp quyền dùng mới theo policy xử lý asset, khác seller delist |
| created_at | timestamptz | Lúc tạo asset |
| updated_at | timestamptz | Lần cập nhật metadata/status gần nhất; không thay payload model đã bán |

Bỏ preview_path/source theo hướng tinh gọn: source chưa cần field riêng nếu hiện chỉ có một pipeline Avaturn; thông tin quyền bán vẫn được xem xét qua listing.rights_note. Preview ảnh/video có thể dùng storage key theo quy ước từ asset_id, hoặc ảnh mặc định; bỏ field không đồng nghĩa tự cấp production VRM cho người chưa mua. Nếu cần nhiều preview tùy biến về sau mới bổ sung metadata phù hợp.

Không tạo một file blob mới cho mỗi buyer. Xóa avatar row của seller không xóa asset còn được listing/avatar khác tham chiếu. Garbage collection phải kiểm tra toàn bộ references và policy erasure, không xóa theo private owner cũ.

### 5.2. avatar_listings — tin đăng trên Marketplace

| Field | Kiểu đề xuất | Mục đích |
|---|---|---|
| id | UUID PK | ID tin đăng, ví dụ LIST-A |
| seller_id | UUID NOT NULL FK users | Người đăng bán và nhận coins; backend lấy từ session, không nhận seller tùy ý từ client |
| asset_id | UUID NOT NULL FK avatar_assets | Model được bán; không đổi sang asset khác trên cùng listing đã được bán |
| title | TEXT NOT NULL | Tên sản phẩm, ví dụ Interviewer Alex |
| description | TEXT nullable | Nội dung giới thiệu buyer nhìn thấy |
| default_personality | TEXT NOT NULL | Personality mặc định để khởi tạo avatar buyer. Lưu độc lập với avatar seller, để seller xóa bản gốc không làm mất cấu hình của listing |
| price_coins | INTEGER NOT NULL >0 | Giá đang bán, khác giá lịch sử buyer đã thanh toán |
| status | enum/check TEXT | draft/pending/active/rejected/delisted/suspended; quy trình ở dưới |
| rights_note | TEXT NOT NULL | Seller cung cấp nguồn/quyền phân phối và phạm vi được phép cho Admin review; riêng tư, không biến lời khai thành bằng chứng provider đã cấp quyền |
| reviewed_by | UUID nullable FK users | Admin thực hiện quyết định duyệt gần nhất |
| reviewed_at | timestamptz nullable | Lúc duyệt/từ chối gần nhất |
| review_note | TEXT nullable | Lý do reject/suspend hoặc ghi chú moderation |
| created_at | timestamptz | Lúc tạo tin |
| updated_at | timestamptz | Lần chỉnh tin/trạng thái gần nhất; dùng kiểm soát stale update/review race theo contract |

Status đề xuất: draft→pending→active hoặc rejected; active→delisted khi seller ngừng bán; suspended khi Admin chặn. Delist chỉ ngừng giao dịch mới, không tự revoke quyền cũ. Relist hoặc sửa giá/nội dung tin active chưa có usecase trực tiếp trong MVP; nếu cần mở phải chốt transition để duyệt lại trước implementation; review phải gắn đúng payload, không approve một bản rồi seller thay nội dung ngầm.

Target constraint đề xuất UNIQUE(asset_id): một asset có một listing sản phẩm trong MVP, tránh đăng cùng model nhiều tin khiến buyer mua lặp. Seller/asset của listing giữ cố định từ lúc tạo: không cần query lịch sử text để quyết định có được đổi không. MVP không có hard-delete listing riêng; dùng trạng thái delist/suspended. Erasure là flow riêng phải xử lý references theo policy. Không thêm source_avatar_id bắt buộc: listing trỏ asset đã quản lý độc lập, không cần giữ avatar inventory nguồn để còn bán được.


## 6. Backend/UI cần thay đổi

- API/domain/repository Marketplace cho listing/catalog/moderation và checkout; permission owner/Admin, email verified/locked account theo conventions hiện có.
- Avatar creation/storage read: asset record mới và resolve file qua asset_id; shared model không bị xóa theo một inventory owner.
- Checkout: lock order tương thích user/inventory/wallet/listing operations, debit+credit+ledger+avatar grant atomic; không upload/copy file trong transaction tiền.
- Duplicate handling: buyer đang có asset thì không cấp/charge lại; cùng key không charge lặp, không replay thành một grant mới sau xóa. Chính sách dùng lại key khác intent phải được review rõ, không parse description tùy tiện làm source of truth.
- FE Marketplace list/detail; My Listings và listing form/status; Admin queue/detail review; nút mua và các trạng thái balance/slot/price/loading/error/retry.
- FE existing inventory/setup/history tái sử dụng, thể hiện model/avatar đã cấp và transaction description, không thêm ownership manager hoặc license-status UI.
- Migration/index/queries/Swagger, unit/integration/browser tests và formal SRS/ERD/UC reconciliation. Không thực hiện code, migration hoặc reset DB trong lượt tổng kết này.

## 7. Chưa đưa vào MVP tối giản

- avatar_purchases/entitlement table và purpose/access_status/avatar_listing_id trên transactions.
- preview_path/source trên avatar_assets; preview theo quy ước hoặc ảnh mặc định, không public production model trước mua.
- Khôi phục miễn phí avatar đã xóa hoặc kho quyền mua vĩnh viễn; chưa có data model đủ để cam kết các usecase đó khi chỉ ghi description.
- Dashboard doanh số theo listing, tìm lại sản phẩm chính xác từ giao dịch bằng FK, history join listing tự động.
- Cash-out, marketplace fee engine, refund/revoke theo từng buyer, gifting/resale/transfer/NFT/auction, ratings/reviews/chat.
- Admin tạo/bán avatar hoặc có wallet cá nhân. Không tự thay marketplace thành admin avatar catalog.

Product owner đã đồng ý tổng kết scope tối giản này, gồm việc rút restore miễn phí, sales dashboard, purchase entitlement và individual revoke khỏi MVP. Không khôi phục các scope đó trong task kỹ thuật nếu chưa có quyết định mới.

## 8. Nguồn asset và quyền bán — phụ thuộc thực tế phải xử lý

Trang chủ Avaturn hiện liên kết tới [Terms of Use](https://docs.google.com/document/d/e/2PACX-1vT5_TR6-MNs29LqI-LLKHvIKHVE0iluuapOpHODGRVDaqyfuCsEgaiE3ZIliI1-FN_-9rxJZ3iVo_jJ/pub) (kiểm tra2026-11-07; tài liệu ghi Last Updated2023-01-26). Mục7 giới hạn bán lại/cấp quyền con. Vì vậy chưa có căn cứ cho phép Marketplace từ gói tích hợp hiện tại; chuyển GLB sang VRM không tự tạo thêm quyền phân phối.

Việc cần làm trước khi bật mua bán thật: xác nhận quyền sử dụng/phân phối áp dụng cho nguồn asset cụ thể, cùng phạm vi người mua được dùng. Có thể thiết kế/prototype bằng asset đã có quyền rõ ràng. Không tự mua gói mới hoặc gửi email cho vendor trong bước lên plan.

Nguồn có khuôn mặt người thật cần đồng ý rõ việc người khác dùng hình ảnh đó. Admin duyệt listing không thể tự cấp quyền mà người bán/provider chưa có. Chỉ bật bán với nguồn asset có quyền phân phối rõ; avatar cá nhân vẫn giữ flow private hiện tại nếu không đủ điều kiện bán.


## 9. Kiểm chứng và bước tiếp theo

Nghiệm thu bắt buộc sau khi triển khai:
- Seller submit→Admin approve→buyer mua→own inventory→interview dùng avatar.
- Active listing/public preview không lộ production file cho người chưa mua.
- Self-purchase, insufficient coins/slot, price mismatch, locked/foreign user, listing bị delist đồng thời checkout.
- Same-key retry/double click/lost response không duplicate debit/grant; buyer đã có asset không mua lặp.
- User xóa bản mua: không có restore miễn phí tự động; có thể mua lại nếu listing active. Giao dịch cũ giữ nguyên; không dùng mô tả để cấp avatar lại.
- Seller xóa bản gốc/delist: buyer vẫn dùng avatar đã cấp; asset GC không xóa file đang được tham chiếu.
- Ledger user→user cân bằng coins, không nhầm COIN trade với tiền thật PayOS; không partial wallet/ledger/inventory state.
- Privacy/account erase, in-use avatar FK và owner permission trên asset signed access vẫn đúng.

Phạm vi/usecases đã duyệt. Task plan tiếp theo cần user duyệt trước tạo Jira Marketplace. Các refinement kỹ thuật (retry/lock/migration/enum/index) không được thay scope 2 bảng mới, 1 FK avatar, 0 cột transaction. Fee/refund/cash-out/individual revoke không tự trở lại scope dưới tên task kỹ thuật.

## 10. Contract kỹ thuật và giới hạn cần thể hiện trong task

### Retry không yêu cầu bảng/cột mới

- Checkout phải nhận key ổn định cho một intent, giữ nguyên qua double-click/timeout; key khác cho lần mua mới sau khi user đã xóa avatar.
- Có thể định nghĩa Idempotency-Key theo dạng có namespace + listing ID + giá xác nhận + nonce, validate khớp request trước khi lookup. Đây là **phương án kỹ thuật để review**, dùng idempotency_key có sẵn (giới hạn hiện tại 128 printable ASCII), không thêm cột và không dùng key làm registry quyền sở hữu/history analytics.
- Khi cùng key đã có successful transaction: không chạy debit/credit/grant lần nữa; trả receipt giao dịch cũ. Avatar đã bị xóa thì replay không cấp lại avatar miễn phí. Same key nhưng intent khác hoặc key từ slot/practice operation phải conflict, không replay nhầm.
- Không dựa vào parse description để khôi phục listing/entitlement hoặc kiểm tra ownership. Description chỉ dùng hiển thị lịch sử. Nếu chưa ratify được contract same-key/different-payload an toàn thì task checkout chưa được nghiệm thu; không lặng lẽ bỏ chống double-charge hoặc tự thêm purchase table.

### Xử lý shared asset

- Mọi avatar row gắn asset_id; created_by của asset xác định người tạo, inventory_id xác định người đang dùng. Buyer không được đăng lại asset chỉ vì có bản trong inventory.
- Original creator sở hữu asset row không đồng nghĩa được đọc inventory riêng của buyer. Signed access vẫn theo avatar thuộc own inventory hoặc context interview/posting đã có quyền.
- Normal listing delist/suspend chỉ ngừng lượt mua mới; không tự đổi asset.status thành blocked và làm buyer mất avatar. blocked là trạng thái kỹ thuật của asset; xử lý khiếu nại/revoke hàng loạt chưa là usecase Marketplace đã duyệt.
- Delete avatar của seller chỉ bỏ row đó. Account erasure phải xóa/anonymize metadata/listing theo policy, giữ asset khi các avatar khác còn tham chiếu; không xóa file theo danh sách model_path của một owner như trước. Asset không còn reference có thể được cleanup có điều kiện, không thêm GC scheduler/table nếu chưa cần.

### Migration và giao diện tương thích

- Đối chiếu tất cả consumer avatars.model_path: inventory/private model access, application/interviewer/public presentation theo quyền, account erase và test fixtures. Target cuối chỉ có avatar_assets.model_path và avatars.asset_id.
- Có thể dùng add/backfill/switch/drop để tránh task đầu phá app hiện tại; compatibility column chỉ chuyển tiếp, không là hai nguồn đường dẫn lâu dài. Chốt cách deploy/dev reset riêng trước thực hiện; lượt spec không reset DB.
- API Marketplace theo envelope/snake_case/error/authorization conventions của repo. Catalog chỉ cho Candidate/Recruiter hợp lệ; không mở thêm capability Guest ngoài Decision11.
- Routes dự kiến: marketplace listing list/detail, own listing create/edit/submit/delist, admin pending/review/suspend, checkout. Đây là contract đề xuất, chưa tuyên bố endpoint có sẵn. Pagination, bounds và stale-review guard phải được chốt trước code.
- UI vi/en, loading/empty/error/retry, keyboard/mobile, giá/slot/coins rõ. Nội dung user/model không tự dịch. Preview không cần preview_path column: dùng key convention theo asset_id hoặc ảnh mặc định, không dùng production model làm preview trước mua.

## 11. Ví dụ nghiệm thu và liên kết

Ví dụ không phí: Nam có 50 coins, Bảo 10 coins. Mua 20 coins → Nam 30, Bảo 30; tạo 1 avatar mới của Nam dùng asset chung, ghi 1 dòng COIN from=Nam,to=Bảo,status=success và description mô tả mua Alex. Retry cùng key vẫn Nam 30/Bảo 30 và không tạo row thứ hai. Xóa avatar Nam rồi retry key cũ không regrant; muốn mua lại dùng checkout mới và key mới nếu listing còn active.

Commands khi implementation: từ api chạy focused Go packages inventory, application, interview, account, wallet và pkg/ledger, make check; integration bằng disposable test DB. Từ frontend chạy bun run lint/typecheck/test/build và E2E các flow Marketplace/inventory/setup/history liên quan. Đây là docs-only update; không tuyên bố đã chạy application tests.

Liên quan: [[03_Decisions/Product-Decisions|Decision 26 và Decision 10 đã superseded]], [[01_Domains/Avatar-Voice/README|Avatar/Inventory]], [[01_Domains/Payment/README|Unified Transactions]], [[02_System/Data-Relationships|Data Relationships]].
