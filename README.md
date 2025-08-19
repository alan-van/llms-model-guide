# Tài liệu API Ollama

## Tổng quan

Repository này chứa tài liệu toàn diện cho API Ollama được host tại `https://llms.uat.galaxy.one/`. API cung cấp quyền truy cập vào các mô hình ngôn ngữ lớn (LLMs) khác nhau để tạo văn bản, hoàn thành cuộc trò chuyện và quản lý mô hình.

## Các mô hình có sẵn

Các mô hình hiện có:

- **gpt-oss:20b** - Mô hình GPT Open Source 20B tham số
- **llama3.1:8b** - Mô hình Llama 3.1 8B tham số  
- **llama3.2:3b** - Mô hình Llama 3.2 3B tham số
- **qwen3:1.7b** - Mô hình Qwen 3 1.7B tham số
- **qwen3:8b** - Mô hình Qwen 3 8B tham số

### ⚠️ Khuyến nghị sử dụng

**Để đảm bảo hiệu suất chung cho tất cả người dùng, chúng tôi khuyến nghị:**

- ✅ **Sử dụng `llama3.2:3b`** làm mặc định cho các tác vụ thông thường và môi trường phát triển
- ⚠️ **Chỉ sử dụng các model lớn hơn** (`gpt-oss:20b`, `llama3.1:8b`, `qwen3:8b`) khi thực sự cần thiết cho các tác vụ đặc thù

**Lý do:**
- `llama3.2:3b` nhẹ (3B), khởi tạo nhanh, tiêu tốn ít tài nguyên
- Giảm tải cho hệ thống chung và tránh ảnh hưởng hiệu năng của người dùng khác
- Chất lượng đầu ra tốt, phù hợp cho đa số tác vụ thường gặp
- Tối ưu chi phí và thông lượng

## Cấu hình cơ bản

- **Base URL**: `$BASE` (thay thế bằng `https://llms.uat.galaxy.one`)
- **Biến Model**: `$MODEL` (thay thế bằng tên mô hình có sẵn)

### Xác thực

API yêu cầu Bearer token trong header `Authorization` cho mọi request.
- Định dạng: `Authorization: Bearer SECRET_XXXX`
- Khuyến nghị: lưu token trong biến môi trường và không commit vào git.
- Với Postman: dùng Authorization type "Bearer Token" với giá trị `{{secret_token}}`.

## Các endpoint API

### 1. Tạo văn bản

Tạo văn bản hoàn chỉnh từ một prompt.

**Endpoint**: `POST $BASE/api/generate`

**Request Body**:
```json
{
  "model": "$MODEL",
  "prompt": "Your prompt here",
  "stream": false,
  "options": {
    "temperature": 0.7,
    "top_p": 0.9,
    "top_k": 40,
    "num_predict": 100,
    "stop": ["\n", "Human:", "Assistant:"]
  }
}
```

**Response**:
```json
{
  "model": "$MODEL",
  "created_at": "2024-01-01T00:00:00Z",
  "response": "Generated text response",
  "done": true,
  "context": [1, 2, 3],
  "total_duration": 1234567890,
  "load_duration": 123456789,
  "prompt_eval_count": 10,
  "prompt_eval_duration": 123456789,
  "eval_count": 50,
  "eval_duration": 1234567890
}
```

### 2. Hoàn thành cuộc trò chuyện

Tạo phản hồi kiểu chat với lịch sử cuộc trò chuyện.

**Endpoint**: `POST $BASE/api/chat`

**Request Body**:
```json
{
  "model": "$MODEL",
  "messages": [
    {
      "role": "system",
      "content": "You are a helpful assistant."
    },
    {
      "role": "user", 
      "content": "Hello, how are you?"
    }
  ],
  "stream": false,
  "options": {
    "temperature": 0.7,
    "top_p": 0.9,
    "top_k": 40,
    "num_predict": 100
  }
}
```

**Response**:
```json
{
  "model": "$MODEL",
  "created_at": "2024-01-01T00:00:00Z",
  "message": {
    "role": "assistant",
    "content": "Hello! I'm doing well, thank you for asking. How can I help you today?"
  },
  "done": true,
  "total_duration": 1234567890,
  "load_duration": 123456789,
  "prompt_eval_count": 10,
  "prompt_eval_duration": 123456789,
  "eval_count": 50,
  "eval_duration": 1234567890
}
```

### 3. Danh sách mô hình

Lấy thông tin về các mô hình có sẵn.

**Endpoint**: `GET $BASE/api/tags`

**Response**:
```json
{
  "models": [
    {
      "name": "gpt-oss:20b",
      "modified_at": "2024-01-01T00:00:00Z",
      "size": 1234567890
    },
    {
      "name": "llama3.1:8b", 
      "modified_at": "2024-01-01T00:00:00Z",
      "size": 1234567890
    }
  ]
}
```

### 4. Hiển thị thông tin mô hình

Lấy thông tin chi tiết về một mô hình cụ thể.

**Endpoint**: `POST $BASE/api/show`

**Request Body**:
```json
{
  "name": "$MODEL"
}
```

**Response**:
```json
{
  "license": "MIT",
  "modelfile": "FROM llama3.2:3b\n...",
  "parameters": "3B",
  "template": "{{ .Prompt }}",
  "system": "You are a helpful assistant."
}
```

### 5. Sao chép mô hình

Tạo bản sao của một mô hình hiện có.

**Endpoint**: `POST $BASE/api/copy`

**Request Body**:
```json
{
  "source": "$MODEL",
  "destination": "my-custom-model"
}
```

### 6. Xóa mô hình

Xóa một mô hình khỏi hệ thống.

**Endpoint**: `DELETE $BASE/api/delete`

**Request Body**:
```json
{
  "name": "$MODEL"
}
```

### 7. Tải mô hình

Tải xuống một mô hình từ registry.

**Endpoint**: `POST $BASE/api/pull`

**Request Body**:
```json
{
  "name": "llama3.2:3b",
  "insecure": false
}
```

### 8. Đẩy mô hình

Tải lên một mô hình lên registry.

**Endpoint**: `POST $BASE/api/push`

**Request Body**:
```json
{
  "name": "$MODEL",
  "insecure": false
}
```

## Ví dụ sử dụng

### Ví dụ Python

```python
import requests
import json

BASE_URL = "https://llms.uat.galaxy.one"
MODEL = "llama3.2:3b"  # Khuyến nghị sử dụng model nhẹ (mặc định) để tối ưu hiệu suất
SECRET = "SECRET_XXXX"  # Nên đọc từ biến môi trường

# Generate text
def generate_text(prompt):
    url = f"{BASE_URL}/api/generate"
    payload = {
        "model": MODEL,
        "prompt": prompt,
        "stream": False,
        "options": {
            "temperature": 0.7,
            "num_predict": 100
        }
    }
    
    response = requests.post(
        url,
        headers={"Authorization": f"Bearer {SECRET}"},
        json=payload
    )
    return response.json()

# Chat completion
def chat_completion(messages):
    url = f"{BASE_URL}/api/chat"
    payload = {
        "model": MODEL,
        "messages": messages,
        "stream": False
    }
    
    response = requests.post(
        url,
        headers={"Authorization": f"Bearer {SECRET}"},
        json=payload
    )
    return response.json()

# Ví dụ sử dụng
prompt = "Giải thích về trí tuệ nhân tạo một cách đơn giản"
result = generate_text(prompt)
print(result["response"])

messages = [
    {"role": "user", "content": "Machine learning là gì?"}
]
chat_result = chat_completion(messages)
print(chat_result["message"]["content"])
```

### Ví dụ JavaScript

```javascript
const BASE_URL = "https://llms.uat.galaxy.one";
const MODEL = "llama3.2:3b";  // Khuyến nghị sử dụng model nhẹ (mặc định) để tối ưu hiệu suất

// Generate text
async function generateText(prompt) {
    const response = await fetch(`${BASE_URL}/api/generate`, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Bearer SECRET_XXXX',
        },
        body: JSON.stringify({
            model: MODEL,
            prompt: prompt,
            stream: false,
            options: {
                temperature: 0.7,
                num_predict: 100
            }
        })
    });
    
    return await response.json();
}

// Chat completion
async function chatCompletion(messages) {
    const response = await fetch(`${BASE_URL}/api/chat`, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Bearer SECRET_XXXX',
        },
        body: JSON.stringify({
            model: MODEL,
            messages: messages,
            stream: false
        })
    });
    
    return await response.json();
}

// Ví dụ sử dụng
generateText("Giải thích về trí tuệ nhân tạo một cách đơn giản")
    .then(result => console.log(result.response));

chatCompletion([
    {role: "user", content: "Machine learning là gì?"}
])
    .then(result => console.log(result.message.content));
```

### Hướng dẫn sử dụng cURL

#### Cài đặt cURL
- **Windows**: Tải từ https://curl.se/windows/
- **macOS**: `brew install curl` hoặc đã có sẵn
- **Linux**: `sudo apt-get install curl` (Ubuntu/Debian) hoặc `sudo yum install curl` (CentOS/RHEL)

#### Các lệnh cURL cơ bản

**1. Tạo văn bản**
```bash
curl -X POST "https://llms.uat.galaxy.one/api/generate" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer SECRET_XXXX" \
  -d '{
    "model": "llama3.2:3b",
    "prompt": "Giải thích về trí tuệ nhân tạo",
    "stream": false,
    "options": {
      "temperature": 0.7,
      "num_predict": 100
    }
  }'
```

**2. Hoàn thành cuộc trò chuyện**
```bash
curl -X POST "https://llms.uat.galaxy.one/api/chat" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer SECRET_XXXX" \
  -d '{
    "model": "llama3.2:3b",
    "messages": [
      {"role": "system", "content": "Bạn là một trợ lý AI hữu ích."},
      {"role": "user", "content": "Xin chào, bạn khỏe không?"}
    ],
    "stream": false
  }'
```

**3. Danh sách mô hình**
```bash
curl -X GET "https://llms.uat.galaxy.one/api/tags"
```

**4. Thông tin mô hình**
```bash
curl -X POST "https://llms.uat.galaxy.one/api/show" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer SECRET_XXXX" \
  -d '{"name": "llama3.2:3b"}'
```

**5. Sao chép mô hình**
```bash
curl -X POST "https://llms.uat.galaxy.one/api/copy" \
  -H "Content-Type: application/json" \
  -d '{
    "source": "llama3.2:3b",
    "destination": "my-custom-model"
  }'
```

**6. Xóa mô hình**
```bash
curl -X DELETE "https://llms.uat.galaxy.one/api/delete" \
  -H "Content-Type: application/json" \
  -d '{"name": "my-custom-model"}'
```

#### Lưu ý khi sử dụng cURL
- Thay thế `$BASE` bằng `https://llms.uat.galaxy.one`
- Thay thế `$MODEL` bằng tên mô hình cụ thể
- Sử dụng `-v` để xem thông tin chi tiết: `curl -v -X POST ...`
- Sử dụng `-o output.json` để lưu kết quả vào file: `curl -o response.json -X POST ...`

### Hướng dẫn sử dụng Postman

#### Cài đặt Postman
1. Tải Postman từ https://www.postman.com/downloads/
2. Tạo tài khoản miễn phí
3. Cài đặt và đăng nhập

#### Thiết lập Collection

**1. Tạo Collection mới**
- Click "New" → "Collection"
- Đặt tên: "Ollama API"
- Thêm description: "API cho các mô hình LLM"

**2. Thiết lập Environment Variables**
- Click "Environments" → "New"
- Thêm biến:
  - `base_url`: `https://llms.uat.galaxy.one`
  - `model`: `llama3.2:3b`
  - `secret_token`: `SECRET_XXXX`

#### Tạo các Request

**1. Request tạo văn bản**
- Method: `POST`
- URL: `{{base_url}}/api/generate`
- Headers:
  - `Content-Type: application/json`
  - `Authorization: Bearer {{secret_token}}`
- Body (raw JSON):
```json
{
  "model": "{{model}}",
  "prompt": "Giải thích về blockchain",
  "stream": false,
  "options": {
    "temperature": 0.7,
    "num_predict": 100,
    "top_p": 0.9
  }
}
```

**2. Request chat completion**
- Method: `POST`
- URL: `{{base_url}}/api/chat`
- Headers:
  - `Content-Type: application/json`
  - `Authorization: Bearer {{secret_token}}`
- Body (raw JSON):
```json
{
  "model": "{{model}}",
  "messages": [
    {
      "role": "system",
      "content": "Bạn là một chuyên gia về công nghệ."
    },
    {
      "role": "user",
      "content": "Giải thích về machine learning"
    }
  ],
  "stream": false,
  "options": {
    "temperature": 0.8,
    "num_predict": 150
  }
}
```

**3. Request danh sách mô hình**
- Method: `GET`
- URL: `{{base_url}}/api/tags`

**4. Request thông tin mô hình**
- Method: `POST`
- URL: `{{base_url}}/api/show`
- Headers:
  - `Content-Type: application/json`
  - `Authorization: Bearer {{secret_token}}`
- Body (raw JSON):
```json
{
  "name": "{{model}}"
}
```

#### Thiết lập Tests (Tùy chọn)

**Test cho response thành công:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response has required fields", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData).to.have.property('model');
    pm.expect(jsonData).to.have.property('response');
});
```

**Test cho chat completion:**
```javascript
pm.test("Chat response is valid", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData.message).to.have.property('role');
    pm.expect(jsonData.message).to.have.property('content');
    pm.expect(jsonData.message.role).to.eql('assistant');
});
```

#### Export/Import Collection
- **Export**: Click "..." trên collection → "Export" → Chọn format JSON
- **Import**: Click "Import" → Chọn file JSON đã export

#### Lưu ý khi sử dụng Postman
- Sử dụng Environment variables để dễ dàng thay đổi base URL và model
- Thiết lập Pre-request Scripts nếu cần xử lý dữ liệu trước khi gửi
- Sử dụng Tests để tự động kiểm tra response
- Lưu các request thường dùng vào collection để tái sử dụng

## Tham chiếu tham số

### Tùy chọn tạo văn bản

| Tham số | Kiểu | Mặc định | Mô tả |
|---------|------|----------|-------|
| `temperature` | float | 0.7 | Điều khiển tính ngẫu nhiên (0.0 = xác định, 1.0 = rất ngẫu nhiên) |
| `top_p` | float | 0.9 | Tham số lấy mẫu nucleus |
| `top_k` | int | 40 | Tham số lấy mẫu top-k |
| `num_predict` | int | 128 | Số lượng token tối đa để tạo |
| `stop` | array | [] | Chuỗi dừng để kết thúc tạo văn bản |
| `seed` | int | -1 | Hạt giống ngẫu nhiên để tái tạo |
| `num_ctx` | int | 2048 | Kích thước cửa sổ ngữ cảnh |
| `repeat_penalty` | float | 1.1 | Hình phạt cho việc lặp lại token |
| `repeat_last_n` | int | 64 | Số token để xem xét cho hình phạt lặp lại |

### Tham số mô hình

| Tham số | Kiểu | Mô tả |
|---------|------|-------|
| `model` | string | Tên mô hình để sử dụng |
| `prompt` | string | Văn bản đầu vào để tạo |
| `stream` | boolean | Có stream response hay không |
| `messages` | array | Mảng tin nhắn chat |

## Xử lý lỗi

API trả về các mã trạng thái HTTP tiêu chuẩn:

- `200` - Thành công
- `400` - Yêu cầu không hợp lệ (tham số không đúng)
- `404` - Không tìm thấy mô hình
- `500` - Lỗi máy chủ nội bộ

Định dạng phản hồi lỗi:
```json
{
  "error": "Mô tả thông báo lỗi"
}
```

## Giới hạn tốc độ

Vui lòng chú ý đến việc sử dụng API và triển khai giới hạn tốc độ phù hợp trong ứng dụng của bạn. API có thể có giới hạn tốc độ để đảm bảo sử dụng công bằng cho tất cả người dùng.

## Hỗ trợ

Để được hỗ trợ kỹ thuật hoặc có câu hỏi về API, vui lòng liên hệ nhóm phát triển hoặc tham khảo tài liệu chính thức của Ollama.

## Giấy phép

API này được cung cấp như hiện tại cho mục đích sử dụng nội bộ. Vui lòng tham khảo giấy phép của từng mô hình riêng lẻ để biết các hạn chế sử dụng. 