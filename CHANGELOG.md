# Changelog

## 2026-08-28

### Documentation
- Added `docs/integrations/` directory with guides for Hermes, Claude Code, and OpenClaw
- Fixed benchmark SVG filename (`longcat-pro` → `longcat-2.0`)
- Updated Chat badge from LongCat-Flash to LongCat-2.0
- Added Windows HuggingFace cache warning for 194-shard download
- Linked integration guides from README

### Known Issues
- API endpoint is invite-only (as of 2026-08)
- No vision/multimodal support in API (web UI only)
- Tool args use dict format (not string) — handled by Hermes adapter

## 2026-06-30

- Initial release
- MIT license
- 1.6T total parameters, ~48B active
- 1M context window via LongCat Sparse Attention
- SGLang deployment support
