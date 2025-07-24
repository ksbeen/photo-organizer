### 1. 인증 (Authentication)
#### 1.1. 회원가입

사용자 정보를 받아 데이터베이스에 저장합니다.

- Endpoint: `POST /api/auth/signup`

- Request Body:

```JSON
{
  "username": "user@example.com",
  "password": "password123"
}
```
- Success Response (`200 OK`):

```JSON
{
  "message": "User registered successfully!"
}
```
- Failure Response (`400 Bad Request`):

```JSON
{
  "error": "Username is already taken!"
}
```
1.2. 로그인

사용자 정보가 유효하면, 앞으로의 요청에 사용할 JWT를 발급합니다.

- Endpoint: `POST /api/auth/signin`

- Request Body:

```JSON
{
  "username": "user@example.com",
  "password": "password123"
}
```
- Success Response (`200 OK`):

```JSON
{
  "token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJ1c2VyQGV4YW1wbGUuY29tIiwiaWF0IjoxNzIxOD...",
  "tokenType": "Bearer"
}
```
- Failure Response (`401 Unauthorized`):

```JSON
{
  "error": "Invalid username or password"
}
```
### 2. 이미지 처리 (Image Processing)
#### Note: 모든 이미지 처리 API는 HTTP 헤더에 Authorization: Bearer <JWT_TOKEN> 형식의 인증 토큰이 필요합니다.

2.1. 이미지 업로드

여러 개의 이미지 파일을 서버에 업로드하여 분석을 요청합니다.

- Endpoint: `POST /api/images/upload`

- Request (`multipart/form-data`):

  - Key: `files`

  - Value: 이미지 파일 배열 (`.jpg`, `.png` 등)

- Success Response (`202 Accepted`):

```JSON
{
  "message": "Upload successful. Image processing has started."
}
```
2.2. 분석 그룹 목록 조회

AI 분석을 통해 생성된 이미지 그룹들의 목록을 조회합니다.

- Endpoint: `GET /api/images/groups`

- Success Response (`200 OK`):

```JSON
{
  "groups": [
    {
      "groupId": 1,
      "groupName": "고양이",
      "imageCount": 15,
      "createdAt": "2025-07-24T20:30:00Z"
    },
    {
      "groupId": 2,
      "groupName": "자동차",
      "imageCount": 8,
      "createdAt": "2025-07-24T20:35:00Z"
    }
  ]
}
```
2.3. 특정 그룹 상세 조회

특정 그룹에 속한 이미지들의 상세 정보를 조회합니다.

- Endpoint: `GET /api/images/groups/{groupId}`

- Success Response (`200 OK`):

```JSON
{
  "groupId": 1,
  "groupName": "고양이",
  "images": [
    {
      "imageId": 101,
      "originalFileName": "cat_photo_1.jpg",
      "newFileName": "고양이_001.jpg",
      "thumbnailUrl": "https://<your-cdn>/thumbnails/cat_001.jpg"
    },
    {
      "imageId": 102,
      "originalFileName": "my_cat.png",
      "newFileName": "고양이_002.png",
      "thumbnailUrl": "https://<your-cdn>/thumbnails/cat_002.png"
    }
  ]
}
```
2.4. 그룹 이름 변경

특정 그룹의 이름을 사용자가 원하는 이름으로 변경합니다.

- Endpoint: `PUT /api/images/groups/{groupId}`

- Request Body:

``` JSON
{
  "groupName": "사랑스러운 냥이들"
}
```
- Success Response (`200 OK`):

```JSON
{
  "message": "Group name updated successfully."
}
```
2.5. 그룹 ZIP 파일 다운로드

하나 또는 여러 그룹을 선택하여 ZIP 파일로 압축해 다운로드합니다.

- Endpoint: `GET /api/images/download/zip`

- Query Parameter: `?groupIds={groupId1},{groupId2}`

- Success Response (`200 OK`):

  - Body: 실제 ZIP 파일 데이터 (`application/zip`)

  - Headers: `Content-Disposition: attachment; filename="classified_photos.zip"`
