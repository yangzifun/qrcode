## API 端点

二维码生成服务的基础 URL：

```
https://qrcode.api.yangzihome.space/qr
```

## 请求方法

```
GET
```

## 查询参数

| 参数名                 | 是否必须 | 类型    | 默认值 | 说明                                                         | 合法值范围                                         |
| :--------------------- | :------- | :------ | :----- | :----------------------------------------------------------- | :------------------------------------------------- |
| `text` 或 `url`        | **是**   | String  | 无     | 要编码的文本内容或 URL。**请务必进行 URL 编码 (`encodeURIComponent`)**。 | 任何字符串                                         |
| `cellSize`             | 否       | Integer | `4`    | 每个二维码“模块”（小方块）的像素大小。值越大，生成的图片尺寸越大。 | `1` 到 `20`                                        |
| `margin`               | 否       | Integer | `0`    | 二维码图案周围的空白边距，以模块数量为单位。`0` 表示无额外边距。 | `0` 到 `10`                                        |
| `errorCorrectionLevel` | 否       | String  | `L`    | 二维码的容错级别，指定二维码可被损坏或遮挡而仍然可扫描的程度。 | `'L'` (低), `'M'` (中), `'Q'` (大部分), `'H'` (高) |
| `typeNumber`           | 否       | Integer | `0`    | 二维码的版本号 (`1` 到 `40`)。`0` 表示由 API 自动选择最小的、能容纳所有数据的版本号。 | `0` 到 `40`                                        |

## 响应

- **成功**: 返回 `image/gif` 类型的 GIF 格式二维码图片。
- **失败**: 返回 `application/json` 类型的错误信息。

### 成功响应示例 (HTTP 200 OK)

请求：

```
GET https://qrcode.api.yangzihome.space/qr?text=Hello%20API!
```

响应：

```
Content-Type: image/gif
(Binary GIF image data)
```

![API示例二维码](https://qrcode.api.yangzihome.space/qr?text=Hello%20API!&cellSize=4&margin=4&errorCorrectionLevel=M)

*上图为使用参数 `text=Hello%20API!&cellSize=4&margin=4&errorCorrectionLevel=M` 生成的示例。*

### 错误响应示例 (HTTP 400 Bad Request)

请求：

```
GET https://qrcode.api.yangzihome.space/qr
```

响应：

```
HTTP/1.1 400 Bad Request
Content-Type: application/json
{
  "error": "Missing `text` or `url` parameter"
}
```

### 错误响应示例 (HTTP 500 Internal Server Error)

如果二维码生成过程中发生内部错误 (例如，尝试编码的数据过大导致超出二维码容量)，可能会返回 500 错误：

```
HTTP/1.1 500 Internal Server Error
Content-Type: application/json
{
  "error": "Failed to generate QR code",
  "details": "code length overflow. (too much data)"
}
```

## 缓存

成功的二维码图片响应会包含 `Cache-Control: public, max-age=604800` 头，表示图片将在浏览器和 CDN 边缘缓存 7 天（604800 秒），以提高性能并减少 Workers 的调用次数。
