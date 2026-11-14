---
title: Roadmap học tập theo Role Profile — đặc tả
date: 2026-11-07
status: backend-implemented-ui-in-progress-acceptance-pending
tags:
  - domain
  - roadmap
  - specification
aliases:
  - Learning Roadmap Spec
  - Roadmap theo Role Profile
---

# Roadmap học tập theo Role Profile

> [!important] Trạng thái và phạm vi (cập nhật 2026-11-14)
> Product owner đã chốt thiết kế và business rule ngày 2026-11-07. **Backend đã triển khai và merge vào application main (2026-11-13; migration 000035, KAN-342..352 và KAN-356). UI đang làm (KAN-353..355). Nghiệm thu end-to-end (KAN-357) chưa xong, nên tính năng chưa được coi là released.** Các mục 1–4, 6–8 là business rule và không đổi. Mục 5, 9.2 vốn là thiết kế kỹ thuật nay đã được hiện thực; chi tiết đã làm, các quyết định ngày 13/11 và truy vết spec → task → test nằm ở **mục 13 (Implementation notes)**. Không tự thêm bảng snapshot/version hoặc thay đổi business rule đã duyệt.

> [!warning] Acceptance: pending KAN-357
> Chưa có bằng chứng nghiệm thu E2E. Khi KAN-357 xong và có bằng chứng release, cập nhật dòng này (không tự suy ra từ trạng thái code).

## 1. Mục tiêu và ví dụ xuyên suốt

Mỗi Role Profile của Candidate có **một Roadmap riêng**. Roadmap biến mục tiêu nghề nghiệp trong requirements đã duyệt thành các mục kiến thức có thứ tự; các lần phỏng vấn practice cung cấp kết quả để theo dõi từng mục.

Ví dụ Role Profile A: **Senior Backend Golang**. Roadmap A có SQL → Go concurrency → System design. User có thể luyện tổng quát A hoặc chọn luyện riêng Go concurrency. Cả hai dùng cùng Role Profile và Roadmap; không phải tạo profile khác để luyện lại một mục.

Ba khái niệm cần tách rõ:

| Khái niệm | Ví dụ | Vai trò |
|---|---|---|
| Mục kiến thức / topic | Go concurrency | Nội dung cần học và được theo dõi trên Roadmap |
| Metric | Technical Accuracy | Phương diện đánh giá câu trả lời trong hệ thống Barem |
| Loại câu / rubric | Giải thích cơ chế và lựa chọn giải pháp | Hướng dẫn chấm tái sử dụng cho nhiều câu |

Một câu practice có **một topic chính**, dùng **một rubric** có nhiều criterion; các criterion tham chiếu metric. Topic không thay metric/rubric. Xem [[01_Domains/Evaluation/Scoring-Rubrics-Spec|Barem điểm và tổng hợp metric]].

## 2. Business rule đã chốt

1. Mỗi Role Profile có một Roadmap. Tạo **bản ghi** Roadmap cùng lúc tạo Role Profile; nội dung các mục chỉ sinh từ requirements **sau khi user duyệt**.
2. Roadmap chỉ nhận kết quả **practice** của chủ sở hữu. Không đưa điểm, nhận xét hoặc tín hiệu suy ra từ recruitment vào Roadmap.
3. FE vẽ tuyến tính theo `position`; thứ tự hiển thị không khóa quyền luyện các mục phía sau.
4. User không sửa trực tiếp cấu trúc/nội dung topic, điểm, trạng thái hoặc threshold trong MVP. AI không tự thêm/xóa/đổi chủ đề sau mỗi phiên; kết quả mới cập nhật điểm/trạng thái và gợi ý ôn tập.
5. Cho phép sinh lại Roadmap trước khi bắt đầu bất kỳ practice session nào của profile. Khi đã bắt đầu một phiên, giữ cấu trúc để lịch sử còn ý nghĩa. Đổi mục tiêu bằng Role Profile mới/Roadmap mới.
6. Luyện tổng quát chọn từ bank của profile. Luyện riêng một topic chỉ chọn câu của topic đó, không thay JD/seniority/mục tiêu gốc.
7. Practice giữ **5 coins/lượt**, mặc định **8 core questions**. Tiếp tục cùng phiên không trừ thêm; một profile giữ tối đa một phiên đang dở theo cơ chế hiện có.
8. Mỗi topic cần tối thiểu **5 câu khác nhau** để có thể đạt yêu cầu. Tổng bank mặc định 50 không chứng minh mọi topic đủ câu; phải kiểm tra phân bổ, độ phủ metric và số câu cần cho phiên focused. Thiếu câu thì bổ sung trước start/charge; lỗi thì không charge, không lấy câu khác topic hoặc hạ ngưỡng đạt ngầm.
9. Khi chọn câu, ưu tiên câu chưa đánh giá và mục chưa đạt trong giới hạn coverage Barem. Không cố định vĩnh viễn cùng một bộ 5 câu và không tự thay ngưỡng đã chốt vì bank thiếu.
10. Tiến độ được tính từ kết quả phỏng vấn hợp lệ; đọc tài liệu/đánh dấu hoàn thành bài tập không làm tăng tiến độ.
11. Câu đã hiển thị mà user chủ động bỏ qua/không trả lời khi hoàn tất phiên nhận **0**. Câu không hỏi/không tới lượt không tính 0. Lỗi AI/STT/transcript/recording không tự coi là trả lời sai để cho 0. Practice hiện không ghi video; quy tắc lỗi recording chỉ bảo đảm không dùng lỗi kỹ thuật làm bằng chứng năng lực nếu cơ chế media thay đổi.
12. Phiên chưa hoàn tất hoặc evaluation lỗi/chưa xong không cập nhật Roadmap. Follow-up nằm trong đánh giá core cha; retry không tạo thêm bằng chứng độc lập.

## 3. Hai loại trạng thái khác nhau

### 3.1. `generation_status`: chuẩn bị nội dung

Lưu trên `roadmaps`:

| Giá trị | Ý nghĩa |
|---|---|
| `pending` | Bản ghi đã tạo; chờ requirements được duyệt hoặc chờ công việc sinh nội dung |
| `generating` | Đang sinh/kiểm tra nội dung và chuẩn bị mapping bank |
| `ready` | Đã có tập topic hợp lệ để hiển thị; điều kiện bank/coverage vẫn được kiểm tra trước mỗi start |
| `failed` | Sinh nội dung lỗi; có đường retry |

Trạng thái này không nói user học giỏi hay đã hoàn thành. Không hoàn thành Roadmap rỗng hoặc đang lỗi sinh nội dung.

### 3.2. `learning_status`: tiến độ học

Backend tính từ kết quả; không lưu thêm cột status/score vào `roadmap_items` ở MVP:

| Đối tượng | Trạng thái | Điều kiện |
|---|---|---|
| Topic | `not_started` | Chưa có câu được đánh giá hợp lệ |
| Topic | `in_progress` | Có kết quả nhưng chưa đủ 5 câu khác nhau, hoặc điểm trung bình < 80 |
| Topic | `achieved` | Đủ 5 câu khác nhau trong cửa sổ và điểm trung bình >= 80 |
| Roadmap | `not_started` | Chưa có topic nào có kết quả |
| Roadmap | `in_progress` | Có kết quả nhưng chưa đạt toàn bộ topic |
| Roadmap | `completed` | Nội dung ready, có ít nhất một topic và tất cả topic achieved |

Khi nội dung chưa ready, UI ưu tiên trạng thái chuẩn bị; `progress_percent` là null, không hiển thị 100% từ phép chia 0/0. `learning_status` là not_started nếu chưa có kết quả.

## 4. Cách tính điểm — đọc phần này trước khi implement

### 4.1. Điểm câu lấy từ Barem, không lấy overall của phiên

Barem tạo điểm normalized của mỗi core question trong `interview_questions.score`:

```text
question_score = 100 × tổng điểm criterion đạt / tổng điểm criterion tối đa
```

Follow-up bổ sung câu trả lời cho core cha. Điểm từng criterion/nhận xét giải thích vì sao câu đạt mức đó.

Barem vẫn tính metric/session overall riêng: criterion → metric theo câu → metric phiên → trọng số → `interviews.score`. Roadmap **không thay công thức đó**. Whole-question score vốn dùng xem chi tiết nay còn là đầu vào theo dõi topic; không được dùng điểm topic để tính ngược overall.

### 4.2. Chọn cửa sổ kết quả của một topic

Thực hiện đúng thứ tự:

1. Chỉ lấy kết quả từ các phiên practice đã hoàn tất, evaluation thành công, cùng profile và topic; bỏ câu không hỏi/lỗi kỹ thuật.
2. Với mỗi `core_question_id`, lấy **kết quả mới nhất**. Một câu được hỏi lại nhiều lần chỉ đóng góp một kết quả hiện tại.
3. Sắp các kết quả còn lại theo thời điểm đánh giá của phiên, chọn tối đa **5 core questions khác nhau gần nhất**. Cùng thời điểm cần tie-break ổn định bằng interview ID/assignment position; không dùng thời điểm job catch-up.
4. Lấy trung bình các điểm câu trong cửa sổ. Chưa có kết quả thì score=null; có 1–4 câu thì hiện điểm tạm kèm số câu, vẫn chưa achieved.
5. So sánh điểm **trước làm tròn hiển thị** với 80. Không nâng 79.99 thành đạt chỉ vì UI làm tròn thành 80. Độ chính xác numeric cụ thể phải theo contract kết quả Barem.

Không lấy trung bình toàn bộ lịch sử; không chỉ lấy phiên gần nhất; không lấy điểm cao nhất mọi thời đại. Kết quả mới thay thế dần kết quả cũ. Cửa sổ không tự hết hạn theo số ngày trong MVP.

### 4.3. Ví dụ nhiều phiên và câu hỏi lặp

INT-01: SQL Q1=60, Q2=80; Go Q3=50.
- SQL có điểm tạm70, 2/5 câu, in_progress.
- Go có điểm tạm50, 1/5 câu, in_progress.

INT-02 luyện riêng Go: Q4=70, Q5=90.
- Go có 3 câu khác nhau Q3/Q4/Q5: (50+70+90)/3=70, 3/5, in_progress.
- SQL giữ nguyên vì không có câu SQL mới.

Khi SQL có 5 câu khác nhau đạt 70,80,90,80,90:
- SQL=(70+80+90+80+90)/5=82 → achieved.
- Sau đó trả lời lại chính câu từng đạt 70, lần mới đạt 20: câu đó đóng góp20 thay70; nếu năm câu này vẫn là cửa sổ hiện tại thì SQL=72 → in_progress.
- Lịch sử từng lần trả lời vẫn xem được; chỉ phép tính điểm hiện tại chọn kết quả mới nhất/cửa sổ 5 câu.

### 4.4. Tiến độ toàn Roadmap

```text
progress_percent = 100 × số topic achieved / tổng số topic
```

Ví dụ SQL 82 (đủ 5 câu, achieved), Go 72 (đủ 5 câu, in_progress), System design chưa được hỏi (not_started):
- Roadmap in_progress, đạt 1/3 mục ≈33.3%.
- Điểm SQL 82 là chất lượng câu trả lời; tiến độ33.3% là tỷ lệ mục đạt yêu cầu.
- SQL cao không bù cho Go chưa đạt hoặc System design chưa được đánh giá.

`achieved` và `completed` phản ánh kết quả hiện tại. Nếu điểm mới kéo một mục xuống dưới80, mục về in_progress; Roadmap completed cũng có thể về in_progress. Không có huy hiệu completed vĩnh viễn trong MVP. Ngưỡng5 câu/80 là quyết định product đã duyệt, không phải chứng nhận năng lực Senior hay kết quả kiểm chuẩn AI.

## 5. Thiết kế dữ liệu: đúng ba bảng mới

Schema dưới đây đã được áp dụng bằng migration 000035 (xem mục 13.1 cho các khác biệt/chi tiết hiện thực). ID dùng UUID theo quy ước hiện tại; các timestamp là timestamptz. Không có bảng skill catalog toàn cục, activity checklist, prerequisite graph hoặc snapshot/version.

### 5.1. `roadmaps`

| Field | Ràng buộc | Ý nghĩa |
|---|---|---|
| `id` | UUID PK | ID Roadmap |
| `role_profile_id` | UUID NOT NULL UNIQUE FK role_profiles | Mỗi profile có một Roadmap, owner suy qua candidate_id |
| `generation_status` | NOT NULL, default pending, enum/check | Trạng thái sinh nội dung tại §3.1 |
| `created_at` | NOT NULL, default now() | Tạo cùng Role Profile |
| `updated_at` | NOT NULL | Lần cập nhật bản ghi, không phải thời điểm đánh giá |

Không nhân đôi user_id, tên nghề hoặc seniority nếu đã đọc được từ Role Profile. Tạo profile và Roadmap row trong cùng transaction để không có profile mới bị thiếu Roadmap.

### 5.2. `roadmap_items`

Mỗi dòng là một topic, ví dụ SQL hoặc Go concurrency.

| Field | Ràng buộc | Ý nghĩa |
|---|---|---|
| `id` | UUID PK | ID topic |
| `roadmap_id` | UUID NOT NULL FK roadmaps | Topic thuộc Roadmap nào |
| `title` | TEXT NOT NULL, không rỗng | Tên hiển thị, ví dụ SQL |
| `description` | TEXT NULL | Phạm vi cần luyện theo requirements; có thể kèm gợi ý ôn tập |
| `position` | INT NOT NULL >=0 | Thứ tự vẽ tuyến tính, không phải trọng số hoặc điều kiện mở khóa |
| `created_at` | NOT NULL, default now() | Lúc tạo topic |
| `updated_at` | NOT NULL | Lần sửa nội dung bởi workflow hợp lệ |

Sắp theo position rồi id để ổn định nếu có position trùng. Không có archived_at, parent_id, item_type, manual status hoặc cột điểm/tiến độ lưu riêng. Candidate không có endpoint sửa item trực tiếp.

Ví dụ: TOP-GO thuộc RM-A, title="Go concurrency", description="Goroutine, channel, synchronization, race condition theo mức Senior", position=2.

### 5.3. `roadmap_assessments`

Một dòng nối **một câu đã đánh giá trong một phiên** với **topic nhận bằng chứng**.

| Field | Ràng buộc | Ý nghĩa |
|---|---|---|
| `id` | UUID PK | ID bản ghi liên kết |
| `roadmap_item_id` | UUID NOT NULL FK roadmap_items | Topic nhận kết quả |
| `interview_id` | UUID NOT NULL FK interviews | Phiên practice nguồn |
| `question_position` | INT NOT NULL | Assignment position của core trong phiên, không phải conversation position của follow-up |
| `created_at` | NOT NULL, default now() | Lúc ghi nhận liên kết; không dùng để chọn kết quả gần nhất |

Ràng buộc `UNIQUE(interview_id, question_position)` và composite FK `(interview_id, question_position)` tới `interview_questions(interview_id, position)`. Một core chỉ có một topic chính. Service/transaction phải bảo đảm interview và item cùng profile/candidate và interview type=practice; FK đơn lẻ không đủ chứng minh điều đó.

Bảng này **không có score, feedback, transcript hoặc expected_answer riêng**. Đọc điểm từ interview_questions và criterion details từ kết quả Barem. Cùng core_question xuất hiện ở nhiều phiên tạo nhiều dòng lịch sử; phép tính §4 chỉ chọn lần mới nhất. Retry cùng một kết quả không được tạo dòng thứ hai.

Ví dụ INT-01 câu position1 và 2 thuộc TOP-SQL: tạo hai assessments; join lại kết quả câu để đọc60 và80. Không dùng riêng core_question_id vì không xác định được lần trả lời.

### 5.4. Hai field mới trên bảng có sẵn

| Bảng / field | Ý nghĩa |
|---|---|
| `core_questions.roadmap_item_id` nullable FK | Một topic chính cho câu practice; câu recruitment không có mapping Roadmap |
| `interviews.focus_item_id` nullable FK | NULL là luyện tổng quát; có ID là luyện riêng topic đó; recruitment phải NULL |

Không thêm interviews.roadmap_id hoặc practice_mode khi đã suy qua role_profile_id/focus_item_id. Kiểm tra bank/JD/profile và topic thuộc cùng owner. Không cho client tự gửi topic của câu hỏi hoặc dữ liệu chấm điểm.

### 5.5. Xóa, sinh lại và bảo toàn lịch sử

- Không có xóa item độc lập trong MVP. Dữ liệu đã có session/question/assessment tham chiếu không bị đổi nghĩa hoặc hard-delete tùy tiện.
- Sinh lại trước phiên đầu tiên được phép thay topic và bank **đồng bộ**, giữ Roadmap ID. Kiểm tra không có bất kỳ practice session nào kể cả đã kết thúc nhưng chưa có điểm; kiểm tra lại dưới lock khi commit.
- Sau khi có session, giữ topic mapping và dữ liệu câu đã sử dụng. Không sửa SQL thành Go trên cùng ID.
- Erasure của chủ tài khoản/profile là flow riêng, xử lý assessment trước các parent/references; quy tắc bất biến không cản quyền xóa dữ liệu theo policy hiện có.
- Dev có thể dùng reset/migration strategy đã được dự án duyệt; spec này không thực hiện reset DB và không yêu cầu tương thích điểm legacy bằng điểm giả.

```mermaid
erDiagram
    ROLE_PROFILES ||--|| ROADMAPS : has
    ROADMAPS ||--o{ ROADMAP_ITEMS : contains
    ROADMAP_ITEMS ||--o{ CORE_QUESTIONS : primary_topic
    INTERVIEWS ||--o{ INTERVIEW_QUESTIONS : assigns
    CORE_QUESTIONS ||--o{ INTERVIEW_QUESTIONS : answered_as
    ROADMAP_ITEMS ||--o{ ROADMAP_ASSESSMENTS : receives
    INTERVIEW_QUESTIONS ||--o| ROADMAP_ASSESSMENTS : evidence
    ROADMAP_ITEMS o|--o{ INTERVIEWS : optional_focus
```

Diagram mô tả schema đã áp dụng ở migration 000035. Quan hệ focus/assessment chỉ áp dụng practice.

## 6. Flow tạo, retry và sinh lại

### Tạo và xác nhận

1. Candidate tạo Role Profile từ JD: transaction tạo profile và Roadmap pending.
2. Phân tích JD; user sửa/review requirements. Chưa sinh nội dung Roadmap/bank từ requirements chưa duyệt.
3. Khi user duyệt, workflow sinh topic theo requirements/seniority/refinement. Backend cấp/kiểm tra IDs và position, giới hạn số mục/độ dài output theo contract kỹ thuật.
4. Generate bank nhận topic IDs được phép, kết hợp rubric/expected_answer theo thiết kế Barem. Validate mọi mapping, độ phủ topic/metric và số câu trước khi dùng.
5. Publish tập topic/bank hợp lệ và trạng thái ready theo transaction/workflow an toàn; API không trả bank/đáp án nội bộ cho Candidate.
6. Lỗi generation có failed/retry rõ; retry không tạo thêm Roadmap hoặc nhiều current bank cho cùng JD.

Không giữ DB transaction mở trong suốt model call. Chi tiết claim/fencing cần ngăn worker cũ ghi đè lần sinh mới, dùng generation_status/updated_at cùng cơ chế lock phù hợp; không tự thêm bảng snapshot/version. Nếu phát hiện cần storage vận hành khác ngoài ba bảng đã chốt, phải nêu rõ để review trước khi mở scope.

### Sinh lại trước lần luyện đầu

- Hành động rõ ràng trên UI, có cảnh báo sẽ thay kế hoạch/câu hỏi nội bộ chưa sử dụng.
- Chỉ owner, cùng requirements đã duyệt, chưa có bất kỳ practice session nào.
- Giữ Roadmap ID; thay tập topic và mapping bank đồng bộ. Không xóa bộ cũ trước khi bộ mới hợp lệ; lỗi phải có retry và không kích hoạt bộ dang dở.
- Start và regenerate phải loại trừ nhau dưới lock cùng profile; không để vừa tạo session vừa xóa câu/topic của phiên đó.
- Nếu requirements/mục tiêu thay đổi sau xác nhận, dùng flow Role Profile mới; không mở lại sửa confirmed Role Profile như hệ quả ngầm của tính năng Roadmap.

## 7. Flow start/resume và sampling

### Luyện tổng quát

Role Profile → Roadmap → Luyện tổng quát → setup → preflight → tạo session/focus=NULL → phỏng vấn → chấm → cập nhật đúng các topic được hỏi.

Ưu tiên mục chưa đạt và câu chưa đánh giá, đồng thời giữ metric coverage trong question_limit của Barem. Không tuyên bố mọi topic đã được đánh giá nếu session chỉ hỏi một phần. Không làm topic chưa được hỏi giảm về 0.

### Luyện theo mục

Roadmap → Go concurrency → Luyện mục này → setup với focus_item_id → chỉ sample câu Go → tạo phiên → chấm → cập nhật Go.

- Owner/topic/profile phải khớp, Roadmap/bank đủ điều kiện, avatar/voice/wallet vẫn được kiểm tra như hiện tại.
- Thiếu câu phù hợp thì sinh bổ sung có giới hạn trước khi charge/start; không dùng SQL để đủ8 câu. Tổng bank 50 chỉ là cấu hình mặc định, không là bảo đảm coverage.
- Thiếu tiền/lỗi generation/lỗi start không tạo debit lẻ. Session+assignments+5 coins debit vẫn atomic; preview/setup/generation không trừ phí practice.
- Generation/bổ sung phải giữ nghĩa các câu đã dùng và không reset tiến độ. Bank vẫn là một current pool, không tạo nhiều Blueprint hoặc snapshot.

### Phiên đang dở và idempotency

Giữ một unfinished practice/profile. Nếu đã có phiên tổng quát, bấm luyện Go không được sửa focus/câu của phiên đó hoặc charge phiên thứ hai. UI giải thích và dẫn user tiếp tục phiên cũ hoặc kết thúc trước khi tạo phiên mới.

Cùng idempotency key và cùng intent trả đúng phiên trước đó. Dùng lại key nhưng đổi focus không được khiến UI hiển thị Go trong khi server trả session tổng quát; contract cần conflict/metadata rõ. Response phải cho biết focus thực tế và đây là phiên tạo mới hay resume. Recruitment không nhận focus và không đổi billing/slot policy.

## 8. Chấm practice, cập nhật và khôi phục sau lỗi

1. Kích hoạt contract Barem cho practice: kết quả từng core/criterion, điểm câu normalized và phân biệt skip/no-answer với unasked/evaluation-error. Đây là dependency cần triển khai, không giả đã có trên main.
2. Chỉ khi phiên hoàn tất và evaluation thành công, lấy câu có kết quả hợp lệ cùng topic mapping để insert assessments trong transaction. UNIQUE bảo vệ retry.
3. Read model tính lại điểm/cửa sổ/trạng thái từ dữ liệu đã lưu. Không gọi AI lần nữa chỉ để cộng/trung bình điểm.
4. Nếu cập nhật Roadmap lỗi sau khi kết quả interview đã lưu, giữ nguyên kết quả interview. Cơ chế catch-up phải phát hiện phiên eligible còn thiếu assessments sau retry/restart, dùng existing System Handler job runner/reconciliation có batch/lock; không dựa vào callback chỉ ở RAM.
5. Catch-up dùng thời điểm kết quả của phiên, không dùng created_at của assessments. Worker xử lý phiên cũ sau phiên mới không làm điểm quay về kết quả cũ.
6. UI thể hiện chấm xong nhưng Roadmap đang cập nhật/chưa cập nhật được; không giả hiển thị0 hoặc completed. Khi thiếu liên kết, read API trả update state tính từ sự chênh lệch kết quả/assessments.
7. Gợi ý ôn tập có thể đọc feedback đã có. Không bắt thêm một AI call sau mỗi phiên để quyết định điểm/trạng thái. Không mở scope AI tự sửa topic/editor.

Trong MVP không có manual score override hoặc UI chấm lại lịch sử. Nếu có cơ chế kỹ thuật sửa kết quả hợp lệ, Roadmap đọc kết quả hiện hành và tính lại; không có snapshot điểm cũ. Dữ liệu evaluation sai/chưa tin cậy không được dùng làm bằng chứng đã đạt.

## 9. UI/UX và API boundary

### 9.1. Màn hình

- **Role Profile Detail:** đường vào Roadmap; thấy pending/generating/failed/retry/ready, không tạo nhầm bản thứ hai.
- **Roadmap tuyến tính:** topic theo position, mô tả/phạm vi, điểm hoặc “chưa đánh giá”, số câu hiện có/tối đa 5, learning_status và lần đánh giá mới nhất. Có nút luyện tổng quát/luyện topic; topic phía sau không bị khóa theo position.
- **Giải thích tiến độ:** đủ 5 câu khác nhau, trung bình>= 80; X/Y mục đạt; trạng thái có thể giảm theo kết quả mới. Điểm tạm1–4 câu không trình bày là đạt.
- **Setup:** giữ topic đã chọn qua điều hướng, retry và start; hiển thị phí5 coins và8 core; resume đúng intent của phiên đang dở.
- **Báo cáo sau phiên:** topic nào vừa có kết quả, vì sao đạt/chưa đạt, trạng thái cập nhật Roadmap và liên kết về Roadmap.
- **Lịch sử topic:** các lần luyện/điểm liên quan, câu nào đóng góp cửa sổ hiện tại; phân biệt lịch sử đầy đủ với5 câu dùng tính hiện tại. Chỉ hiển thị câu/feedback sau đánh giá theo quyền practice report, không preview hidden bank/expected_answer.
- **Sinh lại:** chỉ trước phiên đầu, giải thích tác động, khóa nút khi đang chạy/start; lỗi có retry. Không có topic editor.

UI vi/en theo locale hiện có; không tự dịch nội dung chuyên môn do model/user tạo. Có loading/empty/error/retry, keyboard/mobile và nhãn không chỉ dựa vào màu.

### 9.2. Contract kỹ thuật dự kiến

Các route dưới đã được hiện thực (hợp đồng chi tiết, ví dụ JSON và bảng lỗi nằm trong `api/docs/roadmap-api.md` của repo ứng dụng, cùng Swagger tag `roadmap`); bảng này giữ lại làm ranh giới nghiệp vụ:

| Operation | Boundary |
|---|---|
| GET `/role-profiles/{id}/roadmap` | Owner-only; generation_status, learning_status, progress_percent, completed/total items và items đã sort |
| GET `/role-profiles/{id}/roadmap/items/{item_id}/history` | Owner-only; history phân trang, scores/nguồn kết quả và cờ nằm trong cửa sổ hiện tại; không leak grading metadata |
| POST `/role-profiles/{id}/roadmap/retry` | Owner-only, failed/pending hợp lệ, idempotent; không reset Roadmap ready đã có phiên |
| POST `/role-profiles/{id}/roadmap/regenerate` | Owner-only, đã duyệt requirements, chưa có practice session; chống concurrent start |
| Existing practice start | Bổ sung focus_item_id nullable; response/history trả focus thực tế và kết quả resume/new |

Mỗi item read model gồm id, title, description, position, score nullable, evidence_question_count 0–5, learning_status và thời điểm kết quả mới nhất. Server tính mọi score/status; client không gửi score/criterion/topic mapping của câu. Dùng envelope/snake_case và cách che tài nguyên không thuộc owner của dự án; không tự đặt status code/code lỗi mới ngoài conventions.

## 10. Phạm vi loại trừ và giới hạn

- Không editor Roadmap, manual completion, checklist tính tiến độ, prerequisite unlock hoặc cây kỹ năng nhiều tầng.
- Không Roadmap dùng chung nhiều Role Profile, template marketplace, chia sẻ công khai, quyền Recruiter xem Roadmap Candidate.
- Không recruitment-to-roadmap hoặc suy ra điểm ẩn qua dashboard/export/notification.
- Không thay metric/weights/overall của Barem; không tự thêm metric hoặc tiêu chí từ topic.
- Không bắt AI tự lập lại toàn bộ kế hoạch sau mỗi phiên; không biến ngưỡng 80 thành chứng nhận tuyển dụng.
- Không tự thêm bảng/cột snapshot/version/score cache. Chi tiết storage phát sinh cần được trình rõ.
- Giá trị5 câu và80 đã duyệt. Kiểm tra calibration để hiểu giới hạn và cải thiện câu hỏi/barem; không tự thay các giá trị này trong task kỹ thuật.

## 11. Phụ thuộc và tiêu chí nghiệm thu

### Phụ thuộc

- Barem: cấu hình rubric/criterion/expected_answer, grading per-core, normalized question score và persistence atomic; coverage/core-follow-up grouping. Phải mở rõ cho practice, không chỉ recruitment. Không tạo task Roadmap thay thế các task Barem đã tồn tại.
- Interview start/coin ledger/idempotency/owner scopes và existing System Handler job runner.
- Privacy: account/profile deletion, export và retention có đường xử lý các FK/history mới. Assessment không sao chép transcript/expected_answer; không giữ evidence text ngoài policy cũ.

### Scenarios bắt buộc

| Case | Kết quả cần đạt |
|---|---|
| Tạo profile, chưa duyệt | Có đúng1 Roadmap pending, chưa lộ bank |
| Confirm/retry generation | Có tập topic/mapping hợp lệ, một current bank, không trùng Roadmap |
| General chỉ hỏi SQL | SQL cập nhật; Go giữ nguyên/chưa đánh giá |
| Focus Go | Chỉ core Go;5 coins một lần; resume không đổi focus |
| Đủ bank tổng nhưng thiếu Go | Bổ sung trước start; lỗi không charge/không dùng SQL |
| 0 câu /1–4 câu /5 câu | NULL+not_started / điểm tạm+in_progress / kiểm tra ngưỡng 80 |
| 79.99 và80 | 79.99 chưa đạt;80 đạt nếu đủ 5 câu, so sánh trước làm tròn |
| Cùng câu được trả lời lại | Chỉ điểm mới nhất của câu đóng góp; không tăng số câu khác nhau |
| Có hơn5 câu khác nhau | Chỉ5 câu gần nhất sau bước dedup; history vẫn đầy đủ |
| Score mới giảm | achieved/completed có thể quay về in_progress |
| Một topic đạt, hai topic chưa |1/3 mục đạt, không completed dù điểm mục đạt rất cao |
| Skip/no-answer so với unasked/error | Skip hợp lệ0; unasked không tham gia; lỗi chưa cho điểm/không auto 0 |
| Retry/restart/catch-up đảo thứ tự | Không trùng assessment/charge; chọn kết quả theo event time |
| Regenerate đồng thời start | Chỉ một intent thắng; không có session tham chiếu topic/câu vừa bị thay |
| Foreign Candidate/Recruiter/recruitment | Không xem/sửa history hoặc đưa recruitment evidence vào Roadmap |
| Erasure/export/retention | Không FK orphan, không bản sao private text, không leak recruitment scores |

Các command kiểm chứng khi implementation: từ `api`, chạy `go test ./internal/features/roleprofile/... ./internal/features/questionbank/... ./internal/features/interview/... ./internal/features/evaluation/...`, `make check`, và integration bằng disposable test DB (`make test-integration`, không trỏ DB dev/shared). Từ `frontend`, chạy `bun run lint`, `bun run typecheck`, `bun run test`, `bun run build`, `bun run test:e2e` cho các flow liên quan. Spec-only update không tuyên bố đã chạy application test suite.

## 12. Quyết định và tài liệu liên quan

- [[03_Decisions/Product-Decisions#25. Per-Role-Profile Learning Roadmap (Agreed 2026-11-07; Implementation Pending)|Decision 25]] (heading giữ nguyên để link không gãy; trạng thái hiện hành nằm ở callout dưới heading)
- [[01_Domains/Evaluation/Scoring-Rubrics-Spec|Barem điểm và metric]]
- [[01_Domains/Job-Description/README|JD review/confirmation]]
- [[01_Domains/Interview/README|Practice session và charging]]
- [[02_System/Data-Relationships|Quan hệ dữ liệu]]

Task execution thuộc repo ứng dụng/Jira sau khi user duyệt plan, không ghi trạng thái sprint hay dependency ticket vào Brain. Thay đổi business rule sau này cần cập nhật spec và Decision 25, không sửa ngầm trong implementation.

## 13. Implementation notes (cập nhật 2026-11-14)

> [!info] Phạm vi của mục này
> Mô tả **những gì đã được hiện thực trên main** và các quyết định ngày 13/11. Không đổi business rule ở mục 1–12; nếu có khác biệt giữa spec và code thì code là nguồn sự thật cho hành vi, và khác biệt được ghi ở đây. Hợp đồng chi tiết (field, ví dụ JSON, bảng lỗi, runbook) ở `api/docs/roadmap-api.md` trong repo ứng dụng. Trạng thái: backend merged 2026-11-13; UI đang làm; **Acceptance: pending KAN-357**.

### 13.1. Schema (migration 000035)

- Ba bảng `roadmaps`, `roadmap_items`, `roadmap_assessments` và hai cột `core_questions.roadmap_item_id` (FK, `ON DELETE SET NULL`), `interviews.focus_item_id` (FK `RESTRICT`, CHECK `focus_item_id IS NULL OR type = 'practice'`) đúng như mục 5.
- `roadmaps.role_profile_id` UNIQUE, FK `ON DELETE CASCADE`; `roadmap_items.roadmap_id` FK `ON DELETE CASCADE`; index `(roadmap_id, position, id)`, `position` không unique. `roadmap_assessments`: `UNIQUE (interview_id, question_position)` + composite FK tới `interview_questions` `ON DELETE CASCADE`; `roadmap_item_id` FK `RESTRICT`; index `(roadmap_item_id, interview_id, question_position)`.
- **Hai trigger plpgsql** (đầu tiên của repo) vì FK không chứng minh được "cùng profile": `core_questions.roadmap_item_id` phải thuộc profile của `jd_id` của câu; `interviews.focus_item_id` phải thuộc `role_profile_id` của phiên. Chỉ chạy khi cột mới khác NULL; vi phạm là `check_violation 23514`. Quyết định giữ cả hai (13/11).
- Migration tạo sẵn một Roadmap `pending` cho **mọi Role Profile đã có**, không sinh nội dung. Role Profile mới tạo Roadmap row cùng transaction (CTE trong `CreateRoleProfile`).
- Không có cột score/status/progress/snapshot/version. Fence của một lần sinh là `roadmaps.updated_at` (đặt bằng `clock_timestamp()`); Claim/Fail/Publish chỉ ghi khi giá trị đã đọc không đổi.
- Không thêm index trên `interviews.completed_at` (xem 13.8).

### 13.2. Endpoints

| Operation | Ghi chú hiện thực |
|---|---|
| `GET /role-profiles/{id}/roadmap` | Candidate + owner. Tính mọi score/status khi đọc. Trả thêm `practice_started`, `update_state` (`up_to_date`/`updating`/`delayed`), `pending_result_count`, `window_size` (5), `achieved_threshold` (80). `progress_percent` 2 chữ số thập phân, `null` nếu chưa `ready` hoặc rỗng. |
| `GET /role-profiles/{id}/roadmap/items/{item_id}/history` | Phân trang `limit`/`offset`, mới nhất trước, đánh dấu `in_current_window`; `result` là `scored` hoặc `skipped` (skipped có `question_text = null`, score 0). Lỗi mới `ROADMAP_ITEM_NOT_FOUND` (404). Profile lạ/không tồn tại là `JD_NOT_FOUND`. |
| `POST /role-profiles/{id}/confirm` | Nay **sinh luôn Roadmap**, đồng bộ (mục 13.3). |
| `POST /role-profiles/{id}/roadmap/retry` | Chỉ khi `pending`/`failed` và requirements đã confirm; idempotent (Roadmap `ready` là no-op 200). |
| `POST /role-profiles/{id}/roadmap/regenerate` | Chỉ khi profile **chưa có interview nào**, kể cả phiên đã kết thúc không điểm/abandoned. |
| `POST /interviews` | Thêm `focus_item_id`; response phiên có `focus_item_id` (focus thực lưu) và `resumed` (chỉ ở câu trả lời của start); lịch sử cũng có `focus_item_id`. 201 = tạo mới, 200 = phiên có sẵn. |

### 13.3. Luồng sinh nội dung

1. Tạo Role Profile → Roadmap `pending`. Chưa sinh gì trước khi user duyệt requirements.
2. **Confirm sinh đồng bộ**: bước 1 sinh topic (4–8 topic, tiêu đề ≤80 ký tự, mô tả ≤400 ký tự; model chỉ trả `title`/`description`, backend cấp id và `position`), bước 2 sinh bank ánh xạ vào topic id đó, rồi publish topic + bank + `ready` cùng nhau. Mất khoảng 40–50 giây; **deadline phía server của confirm, retry, regenerate và start có focus là 200 giây** (một hằng số chung).
3. Kích thước bank Roadmap = `max(QUESTION_BANK_SIZE, 5 × số topic)`; `QUESTION_BANK_SIZE` mặc định hiện là **20** (xem 13.9), nên bank 20–40 câu. Bank sinh theo batch, mỗi batch phải trả đủ phần còn thiếu của từng topic; mỗi topic cần ≥5 câu khác nhau.
4. Sinh có **fence**: Claim chuyển `pending/failed → generating` (hoặc nhận lại `generating` quá **5 phút** — lease), Publish/Fail chỉ ghi nếu fence còn khớp, nên worker cũ không ghi đè lần sinh mới. Không giữ DB transaction qua lúc gọi model.
5. **Lỗi**: confirm lỗi để profile ở `review`, Roadmap `failed`, không topic/bank; confirm lại chính là retry. Hai confirm đồng thời chỉ một lần sinh, bên kia nhận `ROADMAP_GENERATING` (409).
6. **Retry** không bao giờ reset Roadmap `ready`. **Regenerate** giữ `roadmap_id`, thay topic (id mới) và toàn bộ bank trong **một transaction**, dưới advisory lock của profile (cùng khóa với practice start); sinh thất bại thì bộ cũ còn nguyên và trạng thái về `ready` (từ `failed`/`pending` thì vẫn `failed`). Nếu một start thắng race lúc commit thì regenerate trả `ROADMAP_REGENERATE_NOT_ALLOWED`.
7. Mỗi lần sinh trừ 1 đơn vị LLM budget (middleware); lỗi `RATE_LIMITED`/`LLM_BUDGET_EXCEEDED` đều 429.

### 13.4. Start, focus, resume và top-up

- **Focus** chỉ cho practice; topic phải thuộc profile (kiểm tra cả khi đã có phiên mở) — sai là `VALIDATION_ERROR`. Roadmap chưa `ready` là `ROADMAP_NOT_READY` (409). Start tổng quát (focus null) trên profile cũ không có Roadmap vẫn chạy như trước.
- **Idempotency**: cùng key + cùng intent trả phiên đầu (200, `resumed: true`, một lần trừ coin). Cùng key + focus khác (kể cả topic ↔ tổng quát) là `INTERVIEW_START_INTENT_CONFLICT` (409), không tạo gì.
- **Resume**: nếu profile đã có phiên practice dở (không key hoặc key khác), start trả **chính phiên đó** (200, `resumed: true`) với focus của nó, bất kể focus được yêu cầu; không trừ phí, không đổi gì. UI phải so `focus_item_id` trả về với điều đã yêu cầu. *Quyết định 13/11:* resume với focus khác trả phiên đang mở kèm metadata (không phải 409).
- **Sampling**: tổng quát lấy từ cả bank; focus chỉ lấy câu của topic. Barem metric cover (best effort, không từ chối, KAN-378) áp dụng **trước**; xếp hạng quyết định phần còn lại và các hòa: hạng 0 = câu chưa đánh giá ở topic chưa đạt, 1 = đã đánh giá ở topic chưa đạt, 2 = chưa đánh giá ở topic đã đạt, 3 = đã đánh giá ở topic đã đạt; câu cũ chưa map topic coi như thuộc topic chưa đạt. Trong cùng hạng thứ tự ngẫu nhiên, nên không có bộ 5 câu cố định vĩnh viễn.
- **Top-up (rule 8)**: khi start có focus vào topic có ít câu hơn `question_limit` và không có phiên nào được resume, hệ thống thực hiện **đúng một lần gọi model** (số câu yêu cầu nâng lên tối thiểu 5 của một topic; câu đã có được đưa vào như dữ liệu và bị loại nếu lặp). Thứ tự: **sau khi giữ budget LLM, trước khi trừ coin, trước khi kiểm tra ví**, ngoài mọi transaction. Câu thêm vào trong transaction ngắn dưới practice start lock và chỉ khi topic vẫn còn thiếu; không đổi id/rubric/mapping của câu hiện có. Top-up lỗi thì không có phiên, không trừ coin (`QUESTION_GENERATION_FAILED`/`INVALID_QUESTION_GENERATION_OUTPUT`, 502); topic vẫn thiếu sau top-up là `QUESTION_BANK_NOT_READY` (409), không bao giờ mượn câu topic khác. Tốn thêm một đơn vị LLM budget (hoàn lại nếu lỗi) và vài giây đến vài chục giây; client cần timeout ≥120 s.
- **Giới hạn đã biết của top-up** (hành vi hiện tại, chưa có quyết định đổi): vì top-up chạy trước kiểm tra ví, Candidate không đủ 5 coin vẫn có thể kích hoạt một top-up rồi nhận `INSUFFICIENT_BALANCE` (câu thêm vẫn hợp lệ, chỉ tốn một lần gọi model); nhiều start focus đồng thời vào cùng topic ngắn có thể mỗi bên sinh một lần (chỉ lần thêm đầu được giữ).
- Start và regenerate loại trừ nhau bằng `pg_advisory_xact_lock(hash("<candidate>:<profile>"))`, cùng khóa với `LockPracticeStart` của interview; có integration test chứng minh.

### 13.5. Ghi nhận kết quả và khôi phục

- **Điều kiện hợp lệ duy nhất** nằm ở `SessionFacts.Eligible()`: phiên `practice`, đã `completed_at`, `evaluation_status = completed`, và **không** `injection_flagged`. Read model, assessment writer và catch-up đều gọi hàm này; SQL lọc trước của catch-up phản chiếu nó và một test fail nếu hai bên lệch.
- **Điểm câu** đọc từ `interview_questions.score` (numeric(5,2), kết quả Barem), qua `roadmap_assessments`. Lấy latest per core question rồi 5 câu khác nhau gần nhất theo `interviews.completed_at` (không dùng `created_at` của assessment). Điểm trung bình so với 80 **trước làm tròn**: tính chính xác bằng phần trăm số nguyên (hundredths), nên 79,996 hiển thị `80` nhưng **chưa đạt**. `ScoreTopic` là nơi duy nhất có cửa sổ 5 câu và ngưỡng 80.
- **Phân loại một core** (`ClassifyCore`): `scored` (đã trả lời và có điểm câu), `skipped` (đã hiển thị tức `asked_at` có giá trị, nhưng phiên không lưu turn nào cho vị trí đó → 0), `not_asked` (chưa hiển thị → không tính), `ungraded` (đã trả lời nhưng không có điểm → không tính). "Đã trả lời" được suy từ **sự tồn tại của turn**, không từ nội dung câu trả lời, để retention xóa text không biến câu đã trả lời thành 0.
- **Hook sau evaluation**: evaluator được bọc bởi `EvaluateThenLink`; sau khi evaluation thành công, các core đủ điều kiện (kể cả core `skipped`, vì điểm câu NULL và Roadmap tự dựng số 0) được insert vào `roadmap_assessments`, idempotent nhờ UNIQUE. Lỗi link không làm mất kết quả interview.
- **Catch-up job `roadmap.catchup`** (System Handler): mỗi `JOBS_ROADMAP_CATCHUP_INTERVAL` (mặc định 1 phút) lấy tối đa `JOBS_ROADMAP_CATCHUP_BATCH_SIZE` (mặc định 100, 1–1000) phiên eligible còn thiếu link, cũ nhất trước, và link lại. Không gọi AI, không giữ queue, advisory lock theo job; lỗi từng phiên có back-off trong bộ nhớ (1 phút nhân đôi tới 1 giờ). Runbook: `api/docs/roadmap-api.md` mục 11.
- **`update_state`**: `up_to_date` khi mọi câu đã đánh giá đều đã link; `updating` khi có câu chưa link và phiên cũ nhất kết thúc <10 phút trước; `delayed` khi quá **10 phút** tính từ `completed_at` (cũng là trạng thái khi job bị tắt). Roadmap hiển thị chỉ tính từ các dòng đã link. *Quyết định 13/11: ngưỡng 10 phút giữ nguyên.*

### 13.6. Quyết định ghi nhận ngày 13/11

| # | Quyết định | Hệ quả |
|---|---|---|
| 1 | Core đã hiển thị nhưng chưa trả lời khi kết thúc phiên = **0**, cả khi `left` lẫn `abandoned`; **không thêm cột đánh dấu** | Hệ thống không phân biệt được bỏ qua có chủ đích với lỗi kỹ thuật (STT/TTS) ở câu hiển thị cuối cùng; tối đa 1 câu/phiên. Đây là **giới hạn được chấp nhận**. |
| 2 | Phiên có `injection_flagged` bị loại khỏi Roadmap (qua `Eligible()`) | Evaluation không tin cậy không phải bằng chứng (rule 12 và mục 8). Đổi chính sách = sửa `Eligible()` và một dòng SQL catch-up. |
| 3 | Role Profile đã luyện practice **trước** khi có Roadmap không có Roadmap; retry trả `ROADMAP_NOT_RETRYABLE` (409); không có bước map bank cũ | Lối đi là Role Profile mới. |
| 4 | Resume với focus khác trả phiên đang mở (200 + metadata), key dùng lại với focus khác mới là 409 | Xem 13.4. |
| 5 | Ngưỡng `delayed` là 10 phút | Hằng số `UpdateDelayAfter`. |
| 6 | Không thêm index `completed_at` | Truy vấn catch-up giới hạn output theo batch nhưng quét mọi phiên practice đã hoàn tất có core đã map; chấp nhận ở quy mô hiện tại. Nếu cần: partial index `interviews(completed_at) WHERE type='practice' AND evaluation_status='completed'` (cần migration). |
| 7 | Giữ hai trigger ở migration 000035 | Xem 13.1. |
| 8 | Sinh topic dùng chung route AI `question_bank` | Không có AI task riêng; cấu hình qua `AI_QUESTION_BANK_*`. |
| 9 | Một hằng số deadline 200 giây cho confirm/retry/regenerate/start có focus; regenerate lỗi từ `ready` quay về `ready` | |

Điểm cần lưu ý: Barem giữ nguyên chính sách "câu không trả lời bị bỏ khỏi report và overall của phiên" (partial-session policy của Barem), trong khi Roadmap cho cùng câu đó 0 ở **điểm theo topic**. Hai consumer khác nhau; số 0 này không đổi report hay overall.

### 13.7. Privacy

Chi tiết ở `docs/privacy-controls.md` (repo ứng dụng). Tóm tắt: ba bảng và hai cột không chứa score, transcript, câu trả lời hay expected answer. Erasure xóa assessment trước, rồi interview, rồi Roadmap của tài khoản (topic đi theo), rồi Role Profile và JD; câu bank mất con trỏ topic rồi bị xóa cùng JD. Xóa Role Profile vẫn bị từ chối (`JD_IN_USE`) khi profile có interview. Export `GET /account/export` thêm `roadmap` cho từng Role Profile (status, topic, kết quả: topic id, interview id, số câu, điểm câu, thời điểm link); không có recruitment, text câu hỏi, câu trả lời hay rubric. Retention không ảnh hưởng điểm Roadmap. Log chỉ có interview id, số lượng và lớp lỗi.

### 13.8. Giới hạn đã biết

- Không phân biệt bỏ qua với lỗi kỹ thuật ở câu hiển thị cuối (13.6 #1).
- Đường REST `POST /interviews/:id/turns` (route vẫn tồn tại, frontend không dùng) không đặt `asked_at` (chỉ lần phát câu hỏi trực tiếp mới đặt); theo ghi chú bàn giao RM-08/RM-11, phiên trả lời qua đó có core chưa trả lời bị coi là `not_asked`, không phải 0. Chưa kiểm bằng test riêng trong lượt tài liệu này.
- Catch-up không có index `completed_at` (13.6 #6).
- Calibration của grader Barem chưa có (xem [[01_Domains/Evaluation/Scoring-Rubrics-Spec|Barem]] mục 13); ngưỡng 5 câu/80 là quyết định product, không phải chứng nhận năng lực.
- Back-off của catch-up nằm trong bộ nhớ, mất khi restart (mọi phiên lỗi được thử lại ngay).

### 13.9. Khác biệt giữa spec và hiện thực (code là nguồn sự thật)

- Rule 7/8 ghi "bank mặc định 50". Giá trị mặc định hiện hành của `question_bank.size` là **20** (hạ ngày 2026-11-11, xem Barem 3.1) và bank Roadmap là `max(size, 5 × topic)`. Rule 8 (mỗi topic ≥5 câu) vẫn đúng và được kiểm tra/top-up ở mục 13.4.
- Mục 9.1: UI Roadmap chưa có trên main ở thời điểm ghi (KAN-353..355 đang làm). Timeout frontend của `confirmRoleProfile`/`analyze` hiện còn 150 000 ms, trong khi server cần tới 200 giây; phải nâng lên ≥200 000 ms khi làm UI.
- `frontend/src/features/interview-setup/api/start-session.ts`: `confirmedStartCodes` chưa có `ROADMAP_NOT_READY`, `INTERVIEW_START_INTENT_CONFLICT`, `QUESTION_BANK_COVERAGE_INSUFFICIENT` — việc của lane UI/setup.
- `LLM_BUDGET_EXCEEDED` trả 429 (một ghi chú bàn giao ghi nhầm 409); `INSUFFICIENT_BALANCE` trả 422.

### 13.10. Truy vết: spec → task → test

Mã task: RM-n tương ứng KAN-(341 + n). Đường dẫn tính từ `api/` trong repo ứng dụng; riêng các đường dẫn bắt đầu bằng tên feature (ví dụ `roadmap/...`, `interview/...`) nằm dưới `api/internal/features/`. File trong `tests/` là integration test (`make test-integration`, DB dùng riêng).

| Mục spec | Task | Code chính | Test / package |
|---|---|---|---|
| 5, 6 (schema, Roadmap row cùng profile) | RM-01 / KAN-342 | `migrations/000035_*`, `roadmap/domain/roadmap.go`, `roadmap/repository` | `roadmap/tests/roadmap_schema_integration_test.go`, `roadmap_generation_integration_test.go`, `roadmap_assessment_erasure_integration_test.go` |
| 6 (sinh topic từ requirements đã duyệt) | RM-02 / KAN-343 | `roadmap/service/generation.go`, `topics_validation.go`, `roadmap/provider/eino.go` | `roadmap/service/topics_validation_test.go`, `generation_test.go`, `roadmap/provider/eino_test.go`, `roadmap/tests/roadmap_confirm_integration_test.go` |
| 2 rule 8, 6 (bank có topic mapping, coverage) | RM-03 / KAN-344 | `questionbank/domain`, `questionbank/service`, `internal/pkg/ai/tasks.go` | `questionbank/service/generate_topics_test.go`, `validation_topics_test.go`, `internal/pkg/ai/tasks_topics_test.go` |
| 2 rule 5, 6 (retry, regenerate) | RM-04 / KAN-345 | `roadmap/service/generation.go`, `internal/handler/roadmap_generation_handler.go` | `roadmap/service/retry_test.go`, `internal/handler/roadmap_generation_handler_test.go`, `roadmap/tests/roadmap_retry_regenerate_integration_test.go` |
| 3, 4, 11 (điểm topic, cửa sổ 5, ngưỡng 80) | RM-05 / KAN-346 | `roadmap/domain/scoring.go` | `roadmap/domain/scoring_test.go` |
| 2 rule 6, 8, 9; 7 (sampling tổng quát/focus, top-up) | RM-06 / KAN-347 | `interview/domain/sampling_preferring.go`, `roadmap_sampling.go`, `roadmap/service/topup.go` | `interview/domain/sampling_preferring_test.go`, `roadmap_sampling_test.go`, `roadmap/service/topup_test.go`, `interview/tests/roadmap_start_integration_test.go`, `roadmap/tests/roadmap_topup_integration_test.go` |
| 7 (start/resume, idempotency, charge một lần) | RM-07 / KAN-348 | `interview/service/service.go`, `interview/repository`, `internal/handler/interview_handler.go` | `interview/service/focus_test.go`, `interview/tests/roadmap_start_integration_test.go` |
| 2 rule 11; 8 mục 1 (kết quả Barem từng câu cho practice) | RM-08 / KAN-349 | `roadmap/domain/practice_result.go`, `evaluation/repository` | `roadmap/domain/practice_result_test.go`, `evaluation/tests/core_result_facts_integration_test.go` |
| 8 mục 2–5 (ghi assessment) | RM-09 / KAN-350 | `roadmap/service/assessment.go`, `roadmap/repository/assessment.go` | `roadmap/service/assessment_test.go`, `roadmap/tests/roadmap_link_integration_test.go` |
| 8 mục 4–6 (catch-up, update_state) | RM-10 / KAN-351 | `roadmap/service/catchup.go`, `internal/config/jobs.go`, `roadmap/domain/read_model.go` | `roadmap/service/catchup_test.go`, `roadmap/tests/roadmap_catchup_integration_test.go`, `internal/config/jobs_test.go` |
| 9.2 (read API, history, quyền owner) | RM-11 / KAN-352 | `roadmap/service/read.go`, `internal/handler/roadmap_handler.go` | `roadmap/service/read_test.go`, `internal/handler/roadmap_handler_test.go`, `roadmap/tests/roadmap_read_integration_test.go` |
| 9.1 (UI) | RM-12..14 / KAN-353..355 | — | **UI chưa merge vào main** |
| 5.5, 11 (erasure/export/retention) | RM-15 / KAN-356 | `account/repository`, `docs/privacy-controls.md` | `account/tests/account_roadmap_integration_test.go` |
| 11 (scenarios nghiệm thu) | RM-16 / KAN-357 | — | **Acceptance: pending KAN-357** |
| Đồng bộ tài liệu | RM-17 / KAN-358 | Brain + `api/docs/roadmap-api.md` + runbook | Review tài liệu với product/tech owner |
