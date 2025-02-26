# Dropbox MCP Server

[![smithery badge](https://smithery.ai/badge/@amgadabdelhafez/Dropbox-MCP-Server)](https://smithery.ai/server/@amgadabdelhafez/Dropbox-MCP-Server)

This is a Model Context Protocol (MCP) server that integrates with Dropbox. It exposes a set of Dropbox operations as MCP tools, allowing MCP-compatible clients to interact with Dropbox.

## Tools

The following tools are available:

### list_files

Lists files and folders in a Dropbox directory.

- **Input**:
  - `path` (string, optional): Path to the folder (defaults to root "")
- **Output**: JSON array of file and folder entries with metadata

### upload_file

Upload a file to Dropbox.

- **Input**:
  - `path` (string, required): Path where the file should be uploaded (e.g., "/folder/file.txt")
  - `content` (string, required): Base64-encoded file content
- **Output**: Confirmation message with the file path

### download_file

Download a file from Dropbox.

- **Input**:
  - `path` (string, required): Path to the file to download
- **Output**: Base64-encoded file content

### delete_item

Delete a file or folder from Dropbox.

- **Input**:
  - `path` (string, required): Path to the file or folder to delete
- **Output**: Confirmation message with the deleted path

### update_access_token

Update the Dropbox access token at runtime.

- **Input**:
  - `token` (string, required): New Dropbox access token
- **Output**: Confirmation message

## Authentication

The server supports two methods of authentication:

1. **Environment Variable**: Set `DROPBOX_ACCESS_TOKEN` in your environment
2. **Runtime Update**: Use the `update_access_token` tool to set or update the token

The server will automatically handle token validation and provide clear error messages if authentication fails.

## Configuration

### Environment Variables

- `DROPBOX_ACCESS_TOKEN` (optional): A valid Dropbox access token
  - If not provided via environment, use the `update_access_token` tool

### MCP Settings Configuration

Add the following to your MCP settings file:

```json
{
  "mcpServers": {
    "dropbox": {
      "command": "node",
      "args": ["/path/to/dropbox-mcp-server/build/index.js"],
      "env": {
        "DROPBOX_ACCESS_TOKEN": "your-token-here" // Optional
      }
    }
  }
}
```

## Usage

1. Install the server by adding the MCP server configuration to your settings file
2. Provide authentication either through:
   - Setting the `DROPBOX_ACCESS_TOKEN` environment variable, or
   - Using the `update_access_token` tool after starting the server
3. Start using the tools:

   ```typescript
   // Example: List files in root directory
   await mcp.useTool("dropbox-mcp-server", "list_files", { path: "" });

   // Example: Upload a file
   await mcp.useTool("dropbox-mcp-server", "upload_file", {
     path: "/test.txt",
     content: Buffer.from("Hello World").toString("base64"),
   });
   ```

## Error Handling

The server provides detailed error messages for common issues:

- Authentication failures (401 errors)
- Invalid paths or parameters (400 errors)
- Missing or expired tokens
- API rate limits or quotas
- Network connectivity issues

## Troubleshooting

If you encounter issues:

1. Check authentication:
   - Verify the access token is valid and not expired
   - Use `update_access_token` to set a new token if needed
2. Verify paths:
   - Paths should be properly formatted (e.g., "/folder/file.txt")
   - Root directory is represented by an empty string ""
3. Check file operations:
   - Ensure you have the necessary permissions
   - Verify base64 encoding/decoding for file content
4. Monitor server logs:
   - The server provides detailed error messages
   - Check response data for API-specific error details

## Development

Built with:

- TypeScript
- Model Context Protocol SDK
- Axios for HTTP requests
- Dropbox API v2

## License

MIT
