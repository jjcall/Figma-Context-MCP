# Depth Modifications for Complete Node Tree Extraction

## Changes Made

### 1. Updated MCP Tool Description (`src/mcp.ts`)
- **Before**: Tool description actively discouraged depth usage: "Do NOT use unless explicitly requested"
- **After**: Tool description now clarifies that omitting depth will fetch all available levels

### 2. Modified Figma Service Methods (`src/services/figma.ts`)

#### `getFile()` method:
- **Before**: No depth parameter sent to Figma API when depth wasn't specified
- **After**: Defaults to depth=50 (Figma's maximum) to ensure complete traversal
- Added logging to show when auto-max depth is applied

#### `getNode()` method:
- **Before**: No depth parameter sent to Figma API when depth wasn't specified
- **After**: Defaults to depth=50 (Figma's maximum) to ensure complete traversal
- Added consistent logging with getFile()

### 3. Added Configuration Support (`src/config.ts`)
- Added `defaultDepth` property to ServerConfig interface
- Set default depth to 50 (Figma's maximum allowed depth)
- Added configuration source tracking for depth settings

## Why These Changes Were Needed

### Original Figma API Behavior
The Figma API has limited default depth traversal when no depth parameter is specified. This means:
- Complex nested designs would be truncated
- Deeply nested components wouldn't be fully extracted
- The AI wouldn't have complete design context

### New Behavior
- **Explicit depth=50**: Forces Figma API to return maximum depth (50 levels)
- **Complete traversal**: Ensures all nodes in complex designs are extracted
- **Backwards compatible**: Still allows manual depth specification when needed
- **Clear logging**: Shows when auto-max depth is being applied

## Impact on Token Usage

While these changes will increase token usage for deeply nested designs, the benefits include:
- **Complete design context**: AI has access to all design elements
- **Consistent behavior**: No surprises from partial data extraction
- **Better AI outputs**: More accurate implementations with full design knowledge

## Environment Configuration

You can set a custom default depth via environment variable:
```bash
FIGMA_DEFAULT_DEPTH=30  # Custom default depth
```

Or via CLI:
```bash
figma-developer-mcp --figma-api-key=YOUR_KEY --default-depth=30
```

## Testing

To verify the changes work:
1. Use a complex Figma file with deeply nested components
2. Call `get_figma_data` without specifying depth
3. Verify all nested elements are present in the response
4. Check logs for "[auto-max]" indicators showing automatic depth application