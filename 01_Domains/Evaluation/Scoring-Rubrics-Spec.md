---
title: Barem điểm và tổng hợp metric — đặc tả
date: 2026-11-07
status: implemented-calibration-open
tags:
  - domain
  - evaluation
  - rubric
  - specification
aliases:
  - Barem điểm
  - Scoring Rubrics Spec
---

# Barem điểm và tổng hợp metric

> [!important] Phạm vi và trạng thái (cập nhật 2026-11-14)
> Hướng sản phẩm dưới đây được chốt qua thảo luận ngày 2026-11-07 và **đã được triển khai trên application main** (2026-11-11 đến 2026-11-12, PR #127 đến #136; mục 13 liệt kê trạng thái hiện thực, traceability và việc còn mở). **Chưa làm: kiểm chuẩn (calibration) grader với chuyên gia; spec này không tuyên bố độ chính xác của AI.** Các chi tiết kỹ thuật ở các mục 4.2, 10, 12 vốn là đề xuất nay đã có hiện thực, xem mục 13 khi có khác biệt. Spec này thay bản đề xuất trước đó có nhiều bảng snapshot/version và phương án overall chỉ lấy trung bình điểm câu.

## 1. Đọc phần này trước: hệ thống thực sự tính gì?

**Giữ metric và trọng số của posting. Barem giúp cho điểm metric có căn cứ cụ thể hơn.**

Trước đây, AI đọc câu trả lời rồi tự cho điểm từng metric từ 0–100. Với thiết kế mới, AI chấm các **tiêu chí cụ thể trong barem**; backend dùng điểm đó tính ra metric, rồi overall.

```text
Điểm crit của từng câu
    → Điểm metric trong từng câu
    → Trung bình metric qua các câu
    → Áp trọng số metric của posting
    → Overall của phiên
```

| Tên | Hiểu đơn giản | Ví dụ |
|---|---|---|
| Metric | Phương diện năng lực muốn đo | Technical Accuracy |
| Rubric / barem | Bộ hướng dẫn chấm tái sử dụng theo loại câu | Giải thích cơ chế và lựa chọn giải pháp |
| Rubric criterion / crit | Một biểu hiện cụ thể cần chấm, có điểm tối đa và mô tả mức đạt | Giải thích đúng cơ chế, tối đa 2 điểm |
| Expected answer | Nội dung chuyên môn tham chiếu, riêng cho từng câu | Câu SQL cần giải thích cơ chế index và chi phí cập nhật |
| Điểm câu | Tổng điểm các crit của một câu, chuẩn hóa về 100 | 7/10 → 70 |
| Điểm metric của phiên | Tổng hợp kết quả của một phương diện qua các câu | Technical Accuracy = 70 |
| Overall | Một điểm tổng của cả phiên, tính từ metric và trọng số | Overall = 62 |

Một crit thuộc **đúng một metric**. Một metric có thể có nhiều crit. Một câu dùng đúng một rubric, nhưng nhiều câu có thể dùng chung rubric. Mỗi câu vẫn có expected_answer riêng.

**Điểm crit là đầu vào tính toán, không chỉ là lời giải thích.** Nhận xét và trích dẫn câu trả lời mới giải thích vì sao crit được mức điểm đó. Tổng điểm của cả câu phục vụ xem chi tiết; nó không được lấy trung bình để thay overall trong thiết kế kết hợp metric này.

## 2. Ví dụ xuyên suốt: từ ba crit đến overall = 62

### 2.1. Barem dùng chung

Rubric R01: **Giải thích cơ chế và lựa chọn giải pháp**.

| Crit | Nội dung | Metric | Điểm tối đa |
|---|---|---|---:|
| C01 | Giải thích đúng cơ chế chính | Technical Accuracy | 2 |
| C02 | Giải thích đúng các giới hạn liên quan | Technical Accuracy | 3 |
| C03 | Đề xuất lựa chọn phù hợp với tình huống | Problem-Solving | 5 |
| | Tổng điểm câu | | 10 |

Tên tiêu chí và điểm tối đa chưa đủ. Mỗi crit có scoring_guide mô tả rõ các điểm nguyên hợp lệ từ 0 đến max_points. Ví dụ C02: 0 = sai/không có nội dung liên quan; 1 = có một nhận định đúng nhưng thiếu phần chính; 2 = giải thích đúng phần lớn giới hạn nhưng thiếu một ý quan trọng đã được hỏi; 3 = đầy đủ và chính xác. Scoring guide phải phù hợp với expected_answer của câu đang chấm, chấp nhận diễn đạt tương đương và phương án đúng khác.

Bloom taxonomy có thể giúp thiết kế mức yêu cầu nhận thức, nhưng không phải metric, không phải thang điểm, không tự nhân trọng số và không là điều kiện bắt buộc chọn barem trong MVP.

### 2.2. Câu hỏi và expected_answer

Q01: “Explain how an SQL index works, discuss its read/write/storage trade-offs, and justify whether you would index the frequently filtered column in this scenario.”

Expected answer là TEXT, ví dụ: giải thích index như cấu trúc phụ trợ cho tra cứu; đọc có thể tránh quét toàn bộ dữ liệu khi phù hợp; ghi phải duy trì index; phát sinh dung lượng; lập luận lựa chọn dựa trên dữ kiện truy vấn và chi phí ghi. Đây là nội dung tham chiếu, **không yêu cầu candidate nói đúng nguyên văn và không tự cộng điểm riêng**.

Q02 có nội dung và expected_answer khác, nhưng vẫn có thể dùng R01. Câu hỏi phải yêu cầu các nội dung mà barem sẽ đánh giá; không trừ điểm vì ứng viên không nói điều chưa được hỏi.

### 2.3. Kết quả chấm từng crit

Các điểm sau là dữ liệu minh họa phép tính, không phải kết quả kiểm chuẩn AI:

| Crit | Metric | Max | Q01 đạt | Q02 đạt |
|---|---|---:|---:|---:|
| C01 | Technical Accuracy | 2 | 2 | 1 |
| C02 | Technical Accuracy | 3 | 2 | 2 |
| C03 | Problem-Solving | 5 | 3 | 2 |

Backend tính điểm metric trong từng câu bằng cách cộng crit cùng metric:

```text
Q01 Technical Accuracy = (2 + 2) / (2 + 3) × 100 = 80
Q01 Problem-Solving    = 3 / 5 × 100             = 60

Q02 Technical Accuracy = (1 + 2) / (2 + 3) × 100 = 60
Q02 Problem-Solving    = 2 / 5 × 100             = 40
```

### 2.4. Metric của cả phiên và overall

Đề xuất mỗi câu đóng góp bằng nhau vào metric mà câu đó thực sự đánh giá:

| Metric | Q01 | Q02 | Trung bình của phiên | Trọng số posting |
|---|---:|---:|---:|---:|
| Technical Accuracy | 80 | 60 | 70 | 60% |
| Problem-Solving | 60 | 40 | 50 | 40% |

```text
Overall = 70 × 60% + 50 × 40% = 62
```

Lưu `score_details`: một dòng Technical Accuracy = 70 và một dòng Problem-Solving = 50 cho phiên. Lưu `interviews.score = 62`. Tên overall_score ở DTO/logic chỉ giá trị này; không phải một cột overall_score mới bắt buộc thêm vào DB.

Điểm tổng từng câu vẫn có thể tính để hiển thị:

```text
Q01 = (2 + 2 + 3) / 10 × 100 = 70
Q02 = (1 + 2 + 2) / 10 × 100 = 50
Trung bình hai điểm câu = 60
```

**Overall vẫn là 62, không phải 60.** Vì overall dùng trọng số metric, còn tổng điểm câu dùng tổng điểm tối đa trong barem. Không cộng/trộn hai công thức này và không dùng trung bình điểm câu làm fallback khi thiếu metric.

### 2.5. Công thức tổng quát

Với earned(q,c), max(c)>0 và metric(c):

```text
question_score(q) = 100 × Σ earned(q,c) / Σ max(c)
  (cộng mọi crit của câu; để hiển thị chi tiết)

question_metric(q,m) = 100 × Σ earned(q,c) / Σ max(c)
  (chỉ cộng crit thuộc metric m trong câu q)

session_metric(m) = mean(question_metric(q,m))
  (các câu có đánh giá được metric m, mỗi câu một lần)

overall = round(Σ session_metric(m) × posting_weight(m)
                / Σ posting_weight(m))
```

Follow-up cung cấp thêm bằng chứng cho core question cha, không thành một câu độc lập trong mẫu số. Không cộng điểm expected_answer riêng. Không nhân lại max_points như một trọng số thứ hai.

Chi tiết làm tròn đề xuất: giữ độ chính xác khi tính question_metric; lưu session_metric ở hai chữ số thập phân, rồi tính overall từ chính các giá trị metric đã lưu và làm tròn half-up về số nguyên. Điểm câu cũng lưu hai chữ số nếu cần. Vì score_details.score hiện là integer, đề xuất này cần đổi kiểu numeric khi triển khai; xác nhận quy tắc làm tròn trước khi lập trình.

## 3. Những quyết định đã chốt và giới hạn MVP

- Hai bảng cấu hình mới: `rubrics`, `rubric_criteria`.
- `core_questions` thêm `rubric_id` và `expected_answer TEXT`; không có bảng question_expected_points.
- `rubric_criteria.metric_id` giữ liên kết tới catalog metrics; giữ metrics_percentage và trọng số posting/equal-split defaults hiện có.
- Recruiter được tạo barem riêng từ đầu hoặc sao chép mẫu hệ thống, tái sử dụng cho các câu/tin của mình; không bị giới hạn chỉ chọn mẫu hệ thống.
- Barem general theo loại câu; expected_answer riêng theo nội dung. Hệ thống đề xuất cả hai lúc generate, recruiter duyệt và chỉnh.
- Không xây snapshot/version tables hay bảng posting_rubric_drafts riêng. Dùng FK và khóa sửa nội dung đã được dùng, không diễn giải lại lịch sử.
- Dữ liệu dev cũ có thể reset khi triển khai. Không cần backfill, chuyển đổi điểm cũ, legacy-mode hay compatibility rollout chỉ để giữ dữ liệu thử nghiệm. Spec này không tự thực hiện reset DB.
- Chưa thêm chức năng sửa điểm AI bằng tay, phúc khảo có điểm riêng, xếp hạng ứng viên hoặc quyết định tuyển dụng tự động.
- Roadmap và Marketplace có spec riêng về sau; không được kéo điểm tuyển dụng vào Roadmap của candidate.

Phần authoring MVP tập trung recruiter/recruitment. Đề xuất dùng chung engine cho practice với system rubrics, không mở quyền candidate xem/sửa bank; phạm vi chuyển practice trong cùng đợt là điểm cần xác nhận trước implementation. Không được làm hỏng practice đang hoạt động chỉ vì reset dữ liệu dev.

### 3.1. Bổ sung đã chốt ngày 2026-11-11 (khi triển khai)

- **Chính sách tính điểm (RB-08):** điểm metric và overall giữ 2 chữ số thập phân; thiếu coverage của một metric đang hoạt động thì không xuất overall (không coi là 0); phiên dở dang vẫn được chấm nếu mọi metric đang hoạt động đều có đủ bằng chứng. Ngưỡng đạt/rớt tuyển dụng vẫn chưa chốt.
- **Giới hạn kỹ thuật (RB-02):** 1–10 criteria mỗi barem; `max_points` là số nguyên 1–10; tên barem ≤120 ký tự, mô tả ≤1000, tên criterion ≤160, scoring_guide ≤2000. Sửa đồng thời dùng `updated_at` làm token, không thêm cột revision. Mã lỗi ổn định: `RUBRIC_NOT_FOUND` (404), `RUBRIC_LOCKED`, `RUBRIC_IN_USE`, `RUBRIC_STALE` (409).
- **Mẫu hệ thống ban đầu:** 4 mẫu, 14 criteria, mỗi mẫu 10 điểm, seed trong migration 000032. *(Cập nhật 2026-11-12: migration 000034 thêm 5 criteria xuyên suốt, nên hiện là 19 criteria với tổng điểm mẫu 14 / 14 / 12 / 10; mẫu đầu không còn bằng ví dụ R01 ở mục 2. Xem 13.2.)*
- **Generate (RB-05):** bank mặc định 20 câu (trước đây 50); một call duy nhất trả câu hỏi cùng `rubric_id` và `expected_answer`. Tập barem được phép là barem system đang hoạt động cộng barem đang hoạt động của chủ posting, tối đa 20 barem × 8 criteria, chữ do người dùng viết được làm sạch và đóng gói như dữ liệu không tin cậy. `expected_answer` tối đa khoảng 80 từ (server nhận tới 100 từ và 1000 ký tự). `rubric_id` ngoài tập cho phép làm cả batch bị từ chối để retry; barem đã archive hoặc không còn thuộc quyền lúc lưu thì câu được lưu không có barem; `expected_answer` trông như injection thì lưu null. Câu thiếu barem hoặc đáp án hợp lệ được đánh dấu chưa đủ cấu hình chấm, không tự xác nhận. DTO của Recruiter có các trường chấm điểm; DTO runtime và Candidate không bao giờ có.
- **Readiness và thu hồi (RB-07):** gửi duyệt hoặc gửi lại bị 409 `GRADING_CONFIG_INCOMPLETE` nếu còn câu chưa đủ cấu hình chấm (barem bị archive sau khi gán cũng tính là thiếu); Admin approve kiểm tra lại dưới cùng khóa posting. Thêm, sửa chữ hoặc xóa câu hỏi đặt lại `review_submitted`; lưu lại đúng chữ cũ thì không thu hồi.
- **Luật khóa barem:** chỉ cần một câu hỏi nào đó đang tham chiếu, barem và criteria của nó bị khóa (không sửa, không hard-delete, kể cả Admin với mẫu system); archive vẫn được và giữ reference; muốn đổi thì sao chép. Bản nới (chỉ khóa khi posting đã approve hoặc đã có session) bị bác vì cần cột thời điểm nộp duyệt và tạo nguy cơ deadlock giữa sửa bank và sửa barem.
- **Trạng thái triển khai (cập nhật 2026-11-14):** toàn bộ phần trên đã nằm trong `main`: nền, generate, readiness và khóa, UI quản lý barem và duyệt bank, chọn câu theo coverage, AI chấm từng crit, bảng kết quả, báo cáo và UI kết quả. **Kiểm chuẩn chưa làm.** Chi tiết ở mục 13. *(Dòng gốc ngày 2026-11-11 ghi các phần này là chưa làm hoặc chờ merge; nó đã lỗi thời.)*

## 4. DB: cấu hình và kết quả là hai loại dữ liệu khác nhau

### 4.1. Hai bảng cấu hình mới

Hai bảng cấu hình và hai cột của `core_questions` đã được áp dụng ở migration 000032 (2026-11-11); bảng kết quả ở 4.2 đã được áp dụng ở migration 000034 (2026-11-12, xem 13.2).

| Bảng | Trường chính | Ý nghĩa |
|---|---|---|
| rubrics | id, name, description, question_type, scope, owner_id, is_active, created_at, updated_at | Mẫu chấm dùng chung. scope = system/recruiter; system có owner_id NULL; recruiter có owner_id trỏ users.id |
| rubric_criteria | id, rubric_id, metric_id, name, max_points, scoring_guide, position | Một crit thuộc một rubric và đúng một metric; scoring_guide TEXT mô tả mức điểm, không phải raw system prompt |

Proposed MVP: max_points là số nguyên dương; điểm đạt là số nguyên từ 0 đến max_points. UI hỗ trợ viết mô tả mức đạt rõ ràng trong scoring_guide. Không thêm bảng mức điểm hay cột JSONB barem toàn khối. Giới hạn số crit/độ dài guide/điểm tối đa kỹ thuật phải chốt trong task hợp đồng và được FE/BE dùng thống nhất.

`core_questions` thêm:

| Field | Kiểu | Quy tắc |
|---|---|---|
| rubric_id | UUID FK → rubrics.id | Có thể thiếu khi còn chuẩn bị; phải hợp lệ trước khi đưa vào phiên/chấp nhận submission |
| expected_answer | TEXT | Có thể là nhiều ý, phương án đúng khác và lỗi hiểu nhầm cần lưu ý; không bắt trả lời nguyên văn |

Loại câu có thể suy từ rubric.question_type/đề xuất generate; không mặc định thêm cột thứ ba vào core_questions. Confirmation của cả bank dùng luồng review_submitted của posting. Nếu muốn lưu trạng thái đã xem từng câu để tiếp tục ở thiết bị khác, đó là mở rộng riêng, không giả vờ đã có field trong thiết kế tối thiểu.

### 4.2. Kết quả chấm phải được lưu, ngoài hai bảng cấu hình

**“Hai bảng mới” là số bảng cấu hình barem, không có nghĩa toàn bộ thay đổi lưu trữ chỉ có hai bảng.** Một score câu không đủ tái dựng điểm từng metric/crit. Đã thêm (migration 000034; khác biệt so với đề xuất ghi ở 13.2) một bảng kết quả relational:

`interview_criterion_scores`:

- interview_id, question_position: FK ghép tới core question đã gán trong interview_questions.
- rubric_criterion_id: FK tới rubric_criteria; phải thuộc barem của source core question.
- points: điểm đạt, kiểm tra 0..max_points theo crit đã khóa.
- evidence_turn_position, evidence, feedback: tham chiếu/bằng chứng/giải thích trong đúng core question và follow-ups của nó. Không dùng quote từ câu khác.
- UNIQUE(interview_id, question_position, rubric_criterion_id).

Các bảng sẵn có:

| Nơi lưu | Dữ liệu |
|---|---|
| interview_criterion_scores | Điểm và bằng chứng từng crit của từng câu trong từng phiên |
| interview_questions.score (field mới) | Tổng điểm của câu đã chuẩn hóa 0–100, phục vụ xem chi tiết |
| score_details.score | Điểm metric của **cả phiên**, UNIQUE(interview_id, metric_id) |
| interviews.score | Một overall của **cả phiên** |
| conversation_turns.feedback | Nhận xét theo lượt hỏi/trả lời như hiện tại |

Việc thêm bảng kết quả là đề xuất kỹ thuật để đáp ứng khả năng giải thích đã bàn, không phải thêm bảng cấu hình/snapshot. Kiểu numeric/precision và FK xóa phải được rà soát trong thiết kế migration. Không lưu điểm candidate vào core_questions vì một câu có thể được nhiều candidate trả lời khác nhau.

## 5. Use case và quyền sở hữu

Các mã dưới đây chỉ là tên dùng trong spec, **không phải ID use case chính thức** và không tự thay đổi số use case trong báo cáo đã đóng băng.

| Actor | Use case mới/thay đổi |
|---|---|
| Admin | Xem catalog, tạo barem hệ thống, chỉnh barem chưa khóa, sao chép barem, ngừng cho chọn/hard-delete mẫu đủ điều kiện |
| Recruiter | Xem mẫu hệ thống và mẫu của mình; tạo barem riêng; sao chép từ mẫu được phép xem; sửa, archive/xóa mẫu của mình theo điều kiện; áp barem cho một/nhiều câu |
| Recruiter | Xem/sửa expected_answer; duyệt cả bộ câu và gán barem; giữ màn hình trọng số metric của posting |
| Recruiter | Xem điểm crit/bằng chứng, điểm câu, metric và overall trong Application Detail |
| Candidate | Không có authoring barem/bank/expected_answer; giữ quyền xem kết quả practice, status-only ở recruitment |

scope/owner phải kiểm tra server-side. Recruiter không sửa mẫu system, không xem/gán/sao chép mẫu private của người khác; kể cả khi đoán được UUID. Copy tạo một rubric và bộ criteria riêng, không đổi source và không chia sẻ quyền sở hữu source.

Admin quản lý nội dung mẫu hệ thống là phạm vi mới được ratify ở đây, không kéo theo quyền sửa trọng số từng posting, rubric private của recruiter, raw LLM prompts hay điểm đã chấm. MVP có thể seed vài mẫu đã kiểm chuẩn để chạy; use case quản lý qua UI vẫn nằm trong backlog đề xuất.

## 6. Generate và duyệt 50 câu mà không viết 50 barem

Mặc định bank size là 20 (hạ từ 50 ngày 2026-11-11) và session question_limit 8. Đó là hai khái niệm khác nhau: recruiter chuẩn bị bank, runtime chỉ lấy một mẫu core questions cộng follow-ups. Không tuyên bố mỗi phiên hỏi hết 50 câu.

```text
JD/requirements đã xác nhận
    + catalog barem system/own đang được phép chọn
    → Generate câu + đề xuất rubric_id + expected_answer
    → Backend validate cấu trúc, ownership và liên kết
    → Lưu bank để recruiter duyệt theo nhóm barem
    → Chỉnh ngoại lệ, xác nhận toàn bộ và gửi duyệt posting
```

Không bắt buộc có một call AI riêng chỉ để classification. Generator có thể trả metadata cùng câu hỏi. AI chỉ chọn ID trong catalog cho phép, không tự tạo barem system, không cấp quyền qua câu chữ trong response. Không có mẫu phù hợp thì để recruiter chọn/tạo mẫu hoặc báo cần hoàn thiện, không gán bừa và tự đánh dấu đã duyệt.

Hệ thống kiểm tra được ID/ownership, thiếu trường, độ dài, crit→metric và giá trị điểm. Nó không chứng minh được expected_answer do AI sinh ra luôn đúng. Recruiter duyệt nội dung chuyên môn; expected_answer chấp nhận lời giải đúng khác, không biến thành dò từ khóa.

Đề xuất initial question_type: giải thích khái niệm/cơ chế, so sánh/lựa chọn, tình huống/debug, kinh nghiệm kỹ thuật. Đây là bộ khởi đầu để review, không phải taxonomy cứng cho mọi ngành.

## 7. UI/UX cần thay đổi

### 7.1. Quản lý barem

Admin có danh sách barem hệ thống với loại câu, trạng thái và trạng thái khóa; form chỉnh tiêu chí, chọn metric, max_points và scoring guide. Recruiter có thư viện “Mẫu hệ thống / Của tôi”, tạo từ đầu hoặc sao chép. Không cho tạo metric tùy ý trong form barem; chọn catalog metric đã có.

Màn hình cho thấy đang sửa mẫu dùng chung cho bao nhiêu câu/tin nháp. Sửa mẫu ảnh hưởng nhiều câu phải có thông báo phạm vi tác động; khóa/sao chép là hành vi nghiệp vụ, không chỉ nút disabled ở frontend.

### 7.2. Chuẩn bị posting/bank

Giữ màn hình trọng số metric hiện tại. Thêm thông tin barem và expected_answer vào bank editor; gom/lọc theo barem, xem barem chung một lần, áp mẫu cho các câu được chọn và sửa expected_answer từng câu. Đổi barem phải xem lại expected_answer cho phù hợp. Không có bảng expected points hay form nhập 50 cấu trúc điểm độc lập.

Readiness phải chỉ rõ: câu thiếu barem/expected_answer, barem inactive không được chọn mới, reference không thuộc quyền, metric cần đo chưa có coverage. Recruiter có thể lưu nháp chưa đủ; gửi duyệt cần đủ cấu hình. Chọn default metric weights vẫn hợp lệ, không bắt nhập custom weights.

Không thêm version/snapshot/withdrawal workflow riêng. Đề xuất khi nội dung chấm của một posting pending thay đổi, xóa review_submitted để quay lại chuẩn bị và yêu cầu submit lại; Admin phải recheck dưới cùng khóa posting để không approve nội dung vừa bị sửa. Chi tiết này cần được kiểm tra cùng transaction thay đổi bank/weights/barem.

### 7.3. Báo cáo

Application Detail hiển thị theo thứ tự dễ hiểu:

1. Overall của phiên và công thức/trọng số đã dùng.
2. Metric của phiên, vd Technical Accuracy 70.
3. Mở từng câu: crit 2/2, 2/3, 3/5 cùng nhận xét và bằng chứng; metric của câu; tổng điểm câu để tham khảo.

Ghi rõ “Điểm câu để xem chi tiết; overall tính theo metric và trọng số”, tránh khiến người đọc tự lấy trung bình điểm câu rồi thấy khác overall. Không dùng nhãn “điểm rubric” để gộp lẫn điểm crit và tổng điểm câu.

Candidate recruitment không thấy điểm crit, metric, overall, expected_answer hay transcript/recording. Candidate practice giữ quyền báo cáo của mình, không được xem toàn bộ bank chưa hỏi. Không render dữ liệu rồi chỉ ẩn bằng CSS.

UI chrome có vi/en theo hệ thống hiện tại; không tự dịch câu hỏi, expected_answer, rubric người dùng nhập hay câu trả lời. Các màn hình có loading/empty/error/retry, unsaved guard, keyboard và mobile states.

## 8. Khóa sửa/xóa mà không cần snapshot

- Mẫu chưa có reference có thể sửa/xóa bởi đúng chủ thể quản lý.
- Chốt 2026-11-11: chỉ cần một câu hỏi nào đó đang tham chiếu, barem và criteria của nó bị khóa (không sửa, không hard-delete, kể cả Admin với mẫu system); archive vẫn được và giữ reference; muốn đổi thì sao chép (xem 3.1). Câu mô tả cũ về việc sửa mẫu đang được câu nháp tham chiếu không còn áp dụng.
- Barem/criteria được một posting đã approve (`open`, `intake_closed`, `closed`) sử dụng, hoặc được session đã bắt đầu sử dụng, là bất biến. Không chỉ khóa đến khi kết thúc tuyển dụng; đã có lịch sử thì không mở lại để sửa.
- Muốn đổi nội dung/điểm/metric mapping của mẫu bị khóa: tạo/copy mẫu khác, áp vào các câu còn được sửa. Không cần version table.
- Archive ngăn gán mới/generate mới, không xóa nội dung hay làm session đã chuẩn bị hợp lệ bị hỏng. Archive không tự động sửa barem của bất kỳ câu nào.
- Core question content, expected_answer và rubric binding cũng khóa khi tin đã mở/đã có session tham chiếu. Không thể bỏ snapshot mà vẫn cho đổi các dữ liệu đang là căn cứ chấm.
- Quyền khóa áp dụng từ server và transaction, kể cả Admin với mẫu hệ thống. Kiểm tra ở UI chỉ hỗ trợ trải nghiệm.
- Privacy erasure là ngoại lệ được kiểm soát: xóa kết quả crit trước khi xóa interview; giải quyết FK bản riêng theo các restrictions tài khoản hiện có. Không dùng bất biến lịch sử để giữ PII trái chính sách.

## 9. Runtime, tính điểm và tình huống thiếu dữ liệu

1. Khi start, chọn core questions từ bank như hiện tại và lưu assignment. Kiểm tra rubric/expected_answer hợp lệ và cấu hình metric đủ dùng; không nhận expected_answer/rubric từ candidate request.
2. Khi complete, dùng cơ chế claim/retry hiện có. Mỗi core question được chấm riêng cùng follow-ups của nó; giữ isolation, bounded concurrency, redaction/rate limit/LLM budget từ PR #120.
3. AI nhận structured data: câu đã hỏi, câu trả lời, rubric criteria/scoring guides, expected_answer của đúng câu. Rubric/recruiter text và model-written follow-ups là untrusted data; không thể ghi đè system rules. Expected_answer không đưa vào lời nói/captions của live interviewer như một đáp án để đọc cho candidate.
4. AI trả criterion ID, điểm đề xuất nằm trong thang đã mô tả, bằng chứng và nhận xét. Backend kiểm tra đúng rubric, đủ/không lặp crit, phạm vi điểm, quote cùng câu và điều kiện lỗi. Backend tính mọi tổng/chuẩn hóa, không nhận overall do AI tự đặt.
5. Lưu kết quả crit, score câu, score_details, feedback và interviews.score/status cùng transaction. Lỗi rollback; replay của completed không ghi lại hay gọi AI lại. Map core-question assignment đúng cả khi follow-up làm conversation position lệch question position.

Metric coverage cần quy tắc riêng: một câu không đánh giá metric m không đồng nghĩa điểm 0; chỉ những câu có crit thuộc m mới tham gia mean(m). Không đổi trọng số còn lại để giấu metric bị thiếu. Đề xuất sampling ngẫu nhiên có đảm bảo coverage metric đang mang trọng số, từ bank đã duyệt; nếu không thể chọn đủ trong question_limit thì báo cấu hình thiếu, không âm thầm tăng số câu hoặc chấm tưởng tượng. Đây là chi tiết sampling cần review trước implementation.

Không trộn “không được hỏi”, “không có câu trả lời”, “candidate cố tình bỏ qua” và “STT/provider lỗi”. Existing Leave/abandon evaluates answered turns không tự có nghĩa câu chưa trả lời được 0. Đề xuất không xuất overall đầy đủ khi một active metric chưa có đủ bằng chứng; lưu trạng thái/lý do thất bại hoặc thiếu dữ liệu qua contract đã chốt ở task tính điểm. Chính sách điểm cho skip/phiên dở dang và trạng thái UI là điểm cần xác nhận, không tự thêm auto-reject hay một trạng thái application mới.

Giữ cờ nghi ngờ injection và kiểm tra evidence hiện có; quote không tồn tại không được trình bày như đã xác minh. Rubric làm rõ tiêu chuẩn nhưng không bảo đảm semantic judgment của AI đúng 100%. Không dùng model self-confidence làm chứng nhận tin cậy.

## 10. API, codebase và phạm vi triển khai

Các route `/rubrics` dưới đây đã tồn tại trong `main` từ 2026-11-11 (RB-02); các route bank/posting/report còn lại đã được mở rộng và hiện thực (xem 13.3 đến 13.6):

- GET /rubrics: Admin/Recruiter được đọc catalog theo scope system/own, phân trang/lọc loại câu và trạng thái; không mặc định mở authoring API cho Candidate.
- POST /rubrics: tạo private với recruiter hoặc system với Admin; validation crit/metric/điểm/guide.
- GET/PATCH/DELETE /rubrics/:id: đọc, sửa nội dung khi chưa khóa, hard-delete khi không có reference; archive bằng PATCH is_active=false.
- POST /rubrics/:id/copies: sao chép source được phép xem thành mẫu mới do caller quản lý.
- Existing bank generate/list/edit/bulk assignment: thêm rubric_id, expected_answer và kiểm tra readiness, giữ ownership.
- Existing posting weights API: giữ nguyên ý nghĩa metric và default; không xóa vì đã thêm barem.
- Existing authorized report API: thêm crit/question/metric explanation; giữ overall field hiện có.

Giữ snake_case, shared response envelope/error codes và Axios trực tiếp gọi Go; không thêm BFF. Ownership và locked conflicts có mã lỗi ổn định; stale concurrent edit cần revision/updated_at check hoặc re-read under lock, không im lặng ghi đè. Catalog metric không nằm trong phạm vi CRUD tùy ý mới.

Extend các module evaluation, questionbank, jobposting, admin và interview hiện có. Không dựng microservice. Cấu trúc Go domain/service/repository/provider, PostgreSQL migrations/sqlc, frontend feature modules/TanStack Query/RHF/Zod và frontend/AGENTS.md giữ nguyên. Snippet kiểu hiện có:

```go
type MetricScore struct {
    MetricID uuid.UUID
    Score    int32
    Evidence string
}
```

Input/output mới cần typed crit IDs và point validation; kiểu score aggregate cần điều chỉnh nếu chọn precision numeric đề xuất ở trên. Không nhét barem vào raw system prompt hay hand-edit file generated sqlc.

## 11. Kiểm thử và điều kiện hoàn thành

- Tái hiện đúng ví dụ Q01/Q02: điểm câu 70/50; metric phiên 70/50; overall 62. Test cố ý bắt lỗi dùng trung bình điểm câu = 60.
- Unit test công thức khi một metric có nhiều crit, max khác nhau, metric không áp dụng, điểm âm/out-of-range/duplicate crit, làm tròn và follow-up không tăng mẫu số.
- CRUD/ownership: Admin system, recruiter own/copy, cấm foreign private, copy không sửa source; hard-delete/archive/locked edit đúng luật.
- Generate/review: output sai rubric ID, sai quyền, thiếu expected_answer, đổi barem/expected_answer cần review lại; bulk apply không làm thay đổi barem nguồn.
- Transaction/race: approve vs edit/archive/template edit; complete retry không ghi trùng; rollback không để partial scores; sampling coverage và câu thiếu dữ liệu theo contract đã ratify.
- Privacy: candidate recruitment không thấy scores/expected_answer qua API/history/export/asset; xóa tài khoản xóa cả criterion evidence; retention purge quotes/feedback mới, không chỉ transcript cũ; không log raw private payload.
- Calibration: domain expert chấm câu trả lời đúng/thiếu/sai/diễn đạt khác/injection/STT noise; so sánh điểm và độ dao động giữa các lần chạy. Bộ reference, ngưỡng sai lệch chấp nhận và owner chuyên môn phải được chốt trước release; không tự tuyên bố độ chính xác tuyệt đối.

Commands tham chiếu từ application repo (chạy khi implementation có thay đổi, không phải yêu cầu reset DB lúc viết spec):

```sh
cd api
go test ./internal/features/evaluation/... ./internal/features/questionbank/...
go test ./internal/features/jobposting/... ./internal/features/interview/...
go test -tags=integration -count=1 -timeout=5m ./internal/...
make sqlc
make check
```

```sh
cd frontend
bun run lint
bun run typecheck
bun run test
bun run build
bun run test:e2e tests/e2e/recruiter-question-bank.spec.ts tests/e2e/recruiter-evaluation-criteria.spec.ts tests/e2e/recruiter-application-detail.spec.ts tests/e2e/admin-moderation.spec.ts tests/e2e/candidate-report.spec.ts
```

## 12. Các điểm kỹ thuật cần xác nhận khi review plan (trạng thái hiện thực ở 13.7)

1. Tên/DDL bảng kết quả crit và kiểu lưu số. Làm tròn 2 chữ số ở mức bộ tính điểm đã chốt (3.1). Đây là phần thêm để lưu căn cứ tính toán, khác hai bảng cấu hình đã chốt.
2. Sampling đảm bảo metric coverage, chính sách skip và trạng thái report tương ứng (quy tắc thiếu coverage và phiên dở dang ở mức bộ tính điểm đã chốt ở 3.1).
3. Phạm vi dùng system rubrics cho practice trong cùng đợt; không cấp candidate quyền authoring hay xem bank.
4. Bộ mẫu ban đầu, giới hạn dữ liệu và bộ kiểm chuẩn/owner/ngưỡng chất lượng.

Không tạo/cập nhật backlog Jira mới trước khi user duyệt bản chia task. Spec không chứa sprint, người được gán việc hoặc tiến độ ticket; các nội dung vận hành đó thuộc Jira theo quy ước Brain.

## Phần sử dụng điểm câu đã chốt sau spec Barem: Roadmap

[[01_Domains/Learning-Roadmap/Learning-Roadmaps-Spec|Decision 25 — Roadmap theo Role Profile]] dùng normalized question score của practice để theo dõi topic qua nhiều phiên. Cửa sổ 5 câu khác nhau (mỗi câu lấy lần mới nhất), mean >= 80 và đủ 5 câu thì topic đạt. Đây là consumer riêng, không thay công thức metric-weighted overall của spec này và không đưa recruitment evidence cho Candidate. Practice activation/per-core persistence là dependency phải triển khai trước cập nhật tiến độ; không giả đã có trên main. Skip/no-answer đã hiển thị là 0 khi hoàn tất; unasked/technical-error không tự thành 0. Các chính sách này đã được duyệt cho Roadmap practice, không tự chốt threshold hoặc partial-session rule cho recruitment.

## 13. Implementation notes (cập nhật 2026-11-14)

> [!info] Cách đọc mục này
> Ghi **những gì đã có trên main** và các khác biệt với đề xuất ở trên. Không đổi business rule hay công thức ở mục 1 đến 12; khi code khác spec thì code là nguồn sự thật cho hành vi. Các quyết định gốc: Decision 13a, 14, 16, 20, 24 trong [[03_Decisions/Product-Decisions|Product Decisions]] (có implementation note, không viết lại). Không có tuyên bố calibration.

### 13.1. Đã triển khai

| Phần | Task | PR | Trạng thái |
|---|---|---|---|
| Schema, mẫu hệ thống (migration 000032) | RB-01 / KAN-325 | #127 | Merged |
| API `/rubrics` (CRUD, copy, archive, `RUBRIC_STALE`) | RB-02 / KAN-326 | #127 | Merged |
| UI mẫu hệ thống (Admin) và thư viện riêng (Recruiter) | RB-03, RB-04 / KAN-327, 328 | #130 | Merged |
| Generate câu hỏi kèm `rubric_id` + `expected_answer` | RB-05 / KAN-329 | #129 | Merged |
| UI duyệt bank theo barem, sửa `expected_answer` | RB-06 / KAN-330 | #132 | Merged |
| Readiness, khóa sửa/xóa, đồng bộ moderation | RB-07 / KAN-331 | #129 | Merged |
| Bộ tính điểm crit → metric → overall (Go thuần) | RB-08 / KAN-332 | #127 | Merged |
| Chọn câu theo metric coverage, map follow-up về core | RB-09 / KAN-333 | #131 | Merged |
| AI chấm từng crit; lưu kết quả; API báo cáo; UI kết quả | RB-10..13 / KAN-334..337 | #133 | Merged (migration 000034) |
| Kiểm tra độ phủ bank: sinh, chỉnh, gửi duyệt, Admin duyệt | RB-17 / KAN-377 | #136 | Merged |
| Practice: bank phủ catalog, sampler cố phủ không từ chối | RB-18 / KAN-378 | #136 | Merged |
| `features/account` chuyển sang sqlc (nợ kỹ thuật) | KAN-379 | #135 | Merged |
| Đối chiếu tài liệu chính thức và nghiệm thu flow | RB-16 / KAN-340 | — | Chưa làm (do owner tài liệu) |
| Kiểm chuẩn grader | — | — | **Chưa làm** (cần bộ tham chiếu do chuyên gia chấm) |

### 13.2. Schema đã áp dụng

- **Migration 000032:** `rubrics`, `rubric_criteria`, `core_questions.rubric_id` và `expected_answer`; 4 mẫu hệ thống, 14 criteria.
- **Migration 000034:** bảng `interview_criterion_scores` với khóa chính `(interview_id, question_position, rubric_criterion_id)`; `points` (số nguyên >= 0), `evidence` kèm `evidence_turn_position` (bắt buộc trừ khi 0 điểm), `feedback`; FK ghép tới `interview_questions` và `conversation_turns` đều `ON DELETE CASCADE`, FK tới `rubric_criteria` `RESTRICT`. Thêm: `interview_questions.score` numeric(5,2); `score_details.score` đổi sang numeric(5,2) và **cho phép NULL** (metric có trọng số nhưng không câu nào đánh giá, không bao giờ coi là 0), thêm `score_details.weight` (trọng số overall đã dùng, kể cả mặc định chia đều vốn không lưu ở đâu) và `question_count`; `interviews.score` numeric(5,2), NULL khi withheld, cùng `score_outcome` (`complete`/`partial`/`withheld`, NULL cho evaluation trước khi có rubric grading) và `score_withheld_reason` (`missing_metric_coverage`, `partial_session`, `no_answers`, `no_gradable_questions`); CHECK giữ outcome, score và reason nhất quán.
- **Khác biệt với đề xuất 4.2:** đề xuất ban đầu chưa có `weight`, `question_count`, `score_outcome`, `score_withheld_reason` và cho `score_details.score` NOT NULL. Chúng được thêm để báo cáo giải thích được overall; chưa có quyết định product riêng cho các cột này (xem 13.7).
- **Overall có 2 chữ số thập phân** (đã chốt ở 3.1), khác đề xuất làm tròn half-up về số nguyên ở mục 2.5; mục 2.5 giữ nguyên để làm ví dụ, hiện thực theo 3.1.
- **Seed:** 000034 thêm 5 criteria xuyên suốt (Answer Relevance, Communication Clarity) vào các mẫu hệ thống (quyết định product 2026-11-12) để phiên practice dùng catalog chia đều không bị withheld chỉ vì hai metric đó. Tổng điểm mẫu hiện 14 / 14 / 12 / 10; Depth vẫn thiếu ở mẫu đầu, Problem-Solving thiếu ở mẫu thứ tư. Đây là thay đổi dữ liệu hệ thống bằng migration nên luật `RUBRIC_LOCKED` không áp dụng; chưa câu nào từng được chấm trước đó nên không có kết quả lưu bị đổi.
- Không snapshot/version table, không manual override; criteria bị khóa ngay khi có câu tham chiếu nên criterion id đủ để đọc metric và điểm tối đa.

### 13.3. Authoring, generate, readiness (RB-02, 05, 07)

- Giới hạn, mã lỗi và luật khóa như ở 3.1. Generate dùng route AI `question_bank`; bank mặc định 20 câu; câu hỏi có barem và đáp án hợp lệ mới được tính là đủ cấu hình chấm.
- Gửi duyệt/gửi lại bị 409 `GRADING_CONFIG_INCOMPLETE` nếu còn câu thiếu cấu hình chấm; Admin approve kiểm tra lại dưới cùng khóa posting; thêm, sửa chữ hoặc xóa câu thu hồi `review_submitted`.

### 13.4. Chọn câu theo coverage (KAN-333, KAN-378)

- **Active metrics** = metric đang dùng khi chấm: trọng số đã lưu có `percentage > 0`; không có trọng số thì cả catalog `metrics` (practice luôn dùng cả catalog); mọi trọng số bằng 0 thì không có metric đang hoạt động và mẫu là ngẫu nhiên thuần.
- **Recruitment (`SampleQuestions`):** nếu có câu nào trong bank có criteria thì mọi active metric phải được phủ bởi criteria của các câu được chọn, nếu không start bị từ chối bằng 409 `QUESTION_BANK_COVERAGE_INSUFFICIENT` (không tạo gì, không trừ). Thứ tự: bộ phủ trước (xếp theo mức phủ mới, rộng nhất trước), rồi lấp ngẫu nhiên; vị trí 1..k (k = kích thước bộ phủ, tối đa `question_limit`) phủ mọi active metric. Barem chỉ tính theo criteria của các câu **được gán**; rubric đã archive vẫn tính.
- **Practice (`SampleQuestionsBestEffort`, KAN-378):** không bao giờ từ chối vì coverage: chọn bộ phủ rộng nhất trong limit rồi lấp ngẫu nhiên; lỗi tìm kiếm thì rơi về ngẫu nhiên. Metric không được phủ bị withheld khỏi báo cáo (`withheld_reason = missing_metric_coverage`, metric đó `score = null`, không overall), không bao giờ là 0. Roadmap xếp hạng câu **sau** bước cover này (xem [[01_Domains/Learning-Roadmap/Learning-Roadmaps-Spec|Roadmap spec 13.4]]).
- Một thuật toán dùng chung (`AnalyzeCoverage`/`CheckCoverage`): greedy ngẫu nhiên rồi BFS chính xác trên tập metric đã phủ; `Passes` đúng bằng tính khả thi của sampler.
- Phiên kết thúc trước k câu đầu có thể thiếu bằng chứng cho một metric: metric đó bị withheld, không chấm 0.
- Ánh xạ turn → core: mỗi turn lưu có `question_number` bằng `interview_questions.position` của core mà nó thuộc về; follow-up dùng chung số của core cha và không làm lệch số câu. `Repository.TurnCoreQuestions` (interview) mở ánh xạ này cho phía hệ thống; việc chấm điểm dựa vào cùng quy ước qua `ListEvaluationTurns`, nên nếu start đổi cách gán thì phải sửa truy vấn đó.

### 13.5. Kiểm tra độ phủ ở gửi duyệt/duyệt (KAN-377)

- Mã lỗi `METRIC_COVERAGE_INCOMPLETE` (409), chỉ Recruiter/Admin, message chung (không lộ tên metric); mã Candidate `QUESTION_BANK_COVERAGE_INSUFFICIENT` giữ nguyên.
- Gửi duyệt và Admin approve (`Decide(open)`) kiểm tra coverage dưới khóa posting, sau kiểm tra readiness, cùng transaction; reject không cần.
- DTO `CoverageReport` (`passes, assessed, covered, total, question_limit, missing_metrics[{id,name,suggested_rubrics}], exceeds_limit, min_questions`) có ở bank Recruiter, kết quả generate/apply-rubric/add/edit, `PostingWeights.coverage` và dossier Admin.
- Generate: đề xuất hợp lệ nhưng không phủ được là "output không thành công": thử lại trong ngân sách retry hiện có; hết lượt thì lưu đề xuất tốt nhất và `coverage` trong response là cảnh báo.
- **Quyết định 2026-11-12:** đổi trọng số (PUT/DELETE) khiến bank không còn phủ thì **thu hồi submission** của posting đang `pending` + `review_submitted` (`submission_withdrawn`); thay đổi mà bank vẫn phủ thì giữ nguyên.
- Vận hành: đổi `INTERVIEW_QUESTION_LIMIT` sau khi duyệt có thể làm bank cũ không còn phủ và không chặn trước được; khi đó recruitment start sẽ bị từ chối bằng `QUESTION_BANK_COVERAGE_INSUFFICIENT`.

### 13.6. Chấm bằng barem và báo cáo (KAN-334..337)

- Mỗi core question (cùng follow-up) được chấm bằng **một call** qua route AI `evaluation`, tối đa 4 call song song. Criteria gửi dưới dạng khóa ngắn; mô hình không thấy trọng số và không tính metric hay overall (Go tính). Backend từ chối phản hồi thiếu, lặp hoặc bịa criterion, điểm ngoài 0..max, hoặc quote không nằm trong chính câu trả lời của câu đó; phản hồi bị từ chối là output không hợp lệ, được retry hoặc chuyển fallback, không bao giờ sửa lại. Evidence được kiểm nghiêm ngặt: quote phải xuất hiện nguyên văn trong câu trả lời của chính câu đó; một quote rút gọn bằng dấu ba chấm (`...`, `…`, `[...]`) chỉ được nhận khi từng đoạn đều là trích nguyên văn đủ dài và các đoạn xuất hiện đúng thứ tự trong cùng một câu trả lời (`locateEvidence` trong `evaluation/service/grading.go`); quote được lưu đúng như mô hình viết, không được sửa.
- Chấm cả recruitment lẫn practice; practice dùng system rubrics. Bank practice được sinh rubric-complete từ system rubrics.
- Kết quả lưu cùng transaction (criterion, điểm câu, `score_details`, feedback, `interviews.score`/outcome); replay phiên đã completed không ghi lại và không gọi AI lại.
- **Báo cáo:** `scoring.method` (`rubric` hoặc `legacy` cho evaluation trước khi có rubric grading), `scoring.outcome` (`complete`, `partial`, `withheld`), `withheld_reason`, số câu được giao/được chấm, `decimals = 2`; breakdown theo metric kèm trọng số đã dùng và số câu; từng câu có trạng thái `graded`/`not_answered`/`not_gradable` cùng điểm criterion. Quyền xem giữ như mục 7.3 (Candidate recruitment chỉ thấy trạng thái).
- **Câu không trả lời** bị bỏ khỏi báo cáo và overall của phiên (partial-session policy của Barem). Riêng Roadmap của Candidate đếm câu đã hiển thị nhưng không trả lời là 0 cho điểm theo topic (quyết định 2026-11-13); hai consumer khác nhau, số 0 đó không đổi báo cáo hay overall.
- Phiên không có câu trả lời nào nay kết thúc `completed` với overall withheld (`no_answers`) thay vì `failed`; mọi câu không có kết quả hợp lệ là `no_gradable_questions`.
- Privacy: erasure tombstone rồi xóa `interview_criterion_scores`; export mang criteria của practice. **Retention transcript chưa xóa `evidence`/`feedback` của criterion** (xem 13.7).

### 13.7. Việc còn mở và điểm cần xác nhận

- **Calibration chưa làm:** cần bộ tham chiếu do chuyên gia chấm, ngưỡng sai lệch chấp nhận và owner chuyên môn trước khi release; không tuyên bố độ chính xác. Promptfoo suite chưa chạy trong lượt kiểm tra KAN-334 (cần key trả phí).
- **Retention:** `RETENTION_TRANSCRIPT_DAYS` chưa xóa quote và feedback của criterion (TODO được ghi trong tài liệu privacy của repo ứng dụng). Spec 11 yêu cầu "retention purge quotes/feedback mới, không chỉ transcript cũ" nên đây là khoảng trống so với spec. Khoảng trống này hiện chưa có hạng mục theo dõi riêng.
- **Bank practice cũ không có rubric:** practice trên các bank đó kết thúc withheld (`no_gradable_questions`); việc dọn dữ liệu dev do team quyết định.
- **Cấu hình mặc định:** giá trị mặc định `INTERVIEW_QUESTION_LIMIT` 8 và bank 20.
- **Các lựa chọn thiết kế của lane chấm điểm chưa có xác nhận product riêng:** các cột bổ sung ở 13.2; kết thúc `completed + withheld(no_answers)` thay vì `failed`; lý do `no_gradable_questions`; evidence nghiêm ngặt; summary phiên = ghép summary từng câu.
- **Hiển thị cho Recruiter:** Candidate bị từ chối start do `QUESTION_BANK_COVERAGE_INSUFFICIENT` chỉ thấy thông báo chung; chưa có cờ "posting này không thể bắt đầu phỏng vấn" cho Recruiter (đề xuất, chưa làm). Sau KAN-377 lỗi này chỉ còn xảy ra khi `INTERVIEW_QUESTION_LIMIT` đổi sau khi duyệt.
- **Frontend glue chưa làm:** `QUESTION_BANK_COVERAGE_INSUFFICIENT` chưa có trong `confirmedStartCodes` của setup (`frontend/src/features/interview-setup/api/start-session.ts`), nên UI hiện thông báo chung.
- **Decision 14** (lấy mẫu không còn ngẫu nhiên thuần khi có rubric) cần được chủ sở hữu Brain cập nhật; lượt này chỉ thêm implementation note vào Decision 13a, 16, 20, 24, 25.
- **Cần reconcile tài liệu chính thức** (SRS/UC/ERD/UI): xem bảng "Needs owner reconciliation" trong báo cáo của lượt KAN-358.

### 13.8. Truy vết: spec → task → test (tóm tắt)

Đường dẫn dưới `api/internal/features/` trong repo ứng dụng.

| Mục spec | Task | Test / package chính |
|---|---|---|
| 2.5, 3.1 RB-08 (công thức, làm tròn, withhold) | KAN-332 | `evaluation/service/rubric_calculator_test.go`, `score_test.go` |
| 3, 8 (CRUD, lock, copy, `RUBRIC_*`) | KAN-325, 326 | `evaluation/service/rubric_service_test.go`, `evaluation/tests/rubric_schema_integration_test.go`, `rubric_api_integration_test.go` |
| 6 (generate với barem + expected_answer) | KAN-329 | `questionbank/service/generate_grading_test.go`, `questionbank/tests/repository_grading_integration_test.go` |
| 7.2 (readiness, khóa, edit bank, moderation) | KAN-330, 331 | `questionbank/tests/readiness_integration_test.go`, `edit_integration_test.go`, `admin/moderation` (approve gate) |
| 9 (coverage khi chọn câu) | KAN-333 | `interview/tests/sampling_integration_test.go`, `interview/tests/coverage_agreement_integration_test.go`, `interview/domain/sampling_test.go` |
| 9 (chấm, lưu, báo cáo) | KAN-334..337 | `evaluation/service/grading_test.go`, `report_test.go`, `evaluation/tests/scoring_integration_test.go`, `interview/tests/lifecycle_regression_integration_test.go` |
| 6, 9 (gate độ phủ, practice best effort) | KAN-377, 378 | `evaluation/domain/coverage_test.go`, `coverage_report_test.go`, `questionbank/tests/coverage_integration_test.go`, `questionbank/service/generate_coverage_test.go`, `generate_practice_coverage_test.go`, `interview/domain/sampling_besteffort_test.go` |
| 11 (privacy: erasure, export) | KAN-334 (một phần), KAN-379 | `interview/tests/privacy_integration_test.go`, `account/tests`; retention còn mở |
| 11 (calibration) | — | **Chưa làm** |
