# Tổng quan về AI Agent

Tài liệu này giới thiệu các thành phần cơ bản của một AI Agent và cách một mô hình ngôn ngữ lớn (LLM) xử lý đầu vào để tạo câu trả lời.

## AI Agent là gì?

Có thể hình dung một AI Agent như sau:

```text
AI Agent = Harness + LLM
```

- **LLM (Large Language Model)** là mô hình ngôn ngữ tạo và hiểu văn bản bằng cách dự đoán token tiếp theo dựa trên ngữ cảnh.
- **Harness** là phần chương trình bao quanh LLM, điều phối cách agent nhận yêu cầu, cung cấp ngữ cảnh, gọi công cụ, kiểm tra kết quả và trả lời.

LLM tạo năng lực ngôn ngữ; harness biến năng lực đó thành một quy trình có thể tương tác với người dùng và, nếu được cấp quyền, thực hiện hành động qua công cụ.

## AI Agent và Harness gồm những gì?

Một harness có thể gồm các phần sau:

- **Vòng lặp điều khiển:** gửi yêu cầu đến LLM, nhận phản hồi, xử lý yêu cầu gọi công cụ rồi tiếp tục khi cần.
- **Quản lý ngữ cảnh:** kết hợp câu hỏi hiện tại với chỉ dẫn, lịch sử hội thoại và thông tin liên quan.
- **Công cụ:** chẳng hạn tìm kiếm, chạy mã hoặc truy cập dữ liệu. Harness quyết định công cụ nào có sẵn và thực thi lời gọi theo quyền được cấu hình.
- **Ranh giới và kiểm tra:** giới hạn hành động, kiểm tra dữ liệu hoặc xác nhận kết quả trước khi trả lời.
- **Giao diện:** chuyển yêu cầu của người dùng vào hệ thống và trình bày kết quả.

Đây là cách chia khái niệm để dễ học; một sản phẩm thực tế có thể tổ chức các phần này khác nhau.

## Context window, harness và người dùng có thể thay đổi gì?

| Thành phần | Ý nghĩa | Có thể ảnh hưởng hoặc cấu hình |
| --- | --- | --- |
| **Context window** | Lượng token tối đa mô hình có thể xử lý trong một lần, tính trên phần ngữ cảnh đầu vào và phần sinh ra theo giới hạn của hệ thống. | Nội dung nằm trong ngữ cảnh hiện tại: chỉ dẫn, câu hỏi, lịch sử và dữ liệu được đưa vào. Khi hội thoại dài, hệ thống có thể phải bỏ bớt, tóm tắt hoặc chọn lọc nội dung cũ. |
| **Harness** | Chương trình điều phối LLM và các thành phần xung quanh. | Người phát triển có thể thiết kế chỉ dẫn hệ thống, quy tắc chọn công cụ, quyền truy cập, cách quản lý lịch sử, số bước và cách xử lý lỗi. |
| **Người dùng** | Người gửi yêu cầu và dữ liệu cho agent. | Có thể nêu mục tiêu, cung cấp ngữ cảnh, đặt định dạng mong muốn và phản hồi để điều chỉnh kết quả. Khả năng sử dụng công cụ vẫn phụ thuộc vào harness và quyền được cấp. |
| **LLM** | Mô hình đã được huấn luyện để xử lý và sinh token. | Trong một ứng dụng thông thường, người dùng điều khiển đầu ra chủ yếu qua yêu cầu và ngữ cảnh; không thể tự sửa trọng số hay kiến trúc mô hình chỉ bằng cách trò chuyện. |

> Context window không đồng nghĩa với “trí nhớ lâu dài”. Nó là giới hạn phần thông tin được đưa vào một lần xử lý. Nếu ứng dụng có lưu nhớ qua nhiều phiên, đó là một cơ chế riêng do hệ thống xây dựng.

## LLM xử lý đầu vào như thế nào?

Ví dụ người dùng nhập câu: **“Thời tiết Hà Nội hôm nay thế nào?”** Một luồng khái quát:

```text
Văn bản đầu vào
      ↓
Tokenizer: chia văn bản thành token
      ↓
Token ID: đổi mỗi token thành mã số
      ↓
Embedding: đổi mã số thành các vector biểu diễn
      ↓
Các lớp Transformer: xử lý quan hệ giữa token trong ngữ cảnh
      ↓
Phân phối xác suất cho token tiếp theo
      ↓
Chọn token → lặp lại cho đến khi gặp điều kiện dừng
      ↓
Giải mã token thành văn bản đầu ra
```

### 1. Tokenizer và token ID

Tokenizer chia văn bản thành **token**. Token có thể là một từ, một phần của từ, dấu câu hoặc khoảng trắng; chúng không nhất thiết trùng với từ trong câu. Sau đó, mỗi token được ánh xạ sang một **token ID** là số nguyên trong bộ từ vựng của mô hình.

### 2. Embedding

Mỗi token ID được ánh xạ thành một **vector embedding** — một dãy số mà mô hình có thể xử lý. Mô hình cũng cần thông tin về vị trí token để hiểu thứ tự trong câu.

### 3. Transformer và ngữ cảnh

Các lớp Transformer biến đổi những vector này và tính toán token nào liên quan đến nhau trong ngữ cảnh. Với mô hình GPT dạng **decoder-only**, mô hình thường dự đoán token tiếp theo dựa trên các token đứng trước nó.

### 4. Sinh token tiếp theo

Ở mỗi bước, mô hình tính điểm hoặc xác suất cho các token có thể xuất hiện tiếp theo. Cách chọn token chịu ảnh hưởng bởi cấu hình sinh, ví dụ mức ngẫu nhiên. Token mới được nối vào chuỗi; mô hình tiếp tục dự đoán cho đến khi đạt giới hạn hoặc điều kiện dừng.

### 5. Giải mã thành văn bản

Các token đầu ra được chuyển ngược thành văn bản để hiển thị. Trong sơ đồ này, “giải mã” là chuyển token ID về nội dung chữ. Trong thuật ngữ kiến trúc Transformer, từ **decoder** có thể chỉ phần mạng sinh token; đó là cách dùng khác của cùng một từ.

> “LLM dự đoán token tiếp theo” mô tả mục tiêu sinh văn bản cốt lõi, nhưng không có nghĩa toàn bộ sản phẩm AI chỉ gồm mô hình dự đoán. Harness có thể bổ sung tìm kiếm, công cụ, bộ nhớ hoặc các bước kiểm tra.

## Agent sử dụng công cụ như thế nào?

Khi câu hỏi cần thông tin bên ngoài hoặc một hành động, harness có thể cho LLM biết những công cụ nào khả dụng. LLM có thể đề xuất gọi công cụ; harness thực thi lời gọi trong phạm vi quyền đã cấp, đưa kết quả trở lại ngữ cảnh, rồi yêu cầu LLM tiếp tục.

```text
Người dùng → Harness → LLM
                ↑       ↓ đề xuất gọi công cụ
                └─ Công cụ → kết quả
```

Ví dụ: agent cần tra cứu thời tiết hiện tại. LLM có thể chọn công cụ thời tiết, harness gửi truy vấn và nhận dữ liệu, sau đó LLM diễn đạt kết quả cho người dùng. Nếu không có công cụ thời tiết, chỉ riêng LLM không thể đảm bảo biết dữ liệu hiện tại.

## Tóm tắt

- **LLM** xử lý token và sinh văn bản dựa trên ngữ cảnh.
- **Harness** cung cấp quy trình, ngữ cảnh, công cụ và ranh giới hoạt động.
- **AI Agent** là ứng dụng kết hợp LLM với harness để giải quyết yêu cầu qua một hoặc nhiều bước.
- Người dùng ảnh hưởng mạnh đến câu trả lời qua mục tiêu và thông tin cung cấp; khả năng hành động phụ thuộc vào thiết kế và quyền của harness.

## Từ khóa

| Thuật ngữ | Diễn giải ngắn |
| --- | --- |
| LLM | Mô hình ngôn ngữ lớn |
| Harness | Lớp chương trình điều phối mô hình và công cụ |
| Context window | Giới hạn token xử lý trong một lần |
| Tokenizer | Thành phần chia văn bản thành token và ánh xạ token ID |
| Embedding | Vector biểu diễn token để mô hình xử lý |
| Transformer | Kiến trúc mạng nơ-ron phổ biến trong LLM hiện đại |
| Token generation | Quá trình sinh lần lượt token đầu ra |
