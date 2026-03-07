# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [1.1.0] - 2026-03-06

### Added

- `manage-intents` skill for creating and managing persistent saved searches with webhook notifications
- Intent tools documentation in `listing-api` skill (list, get, create, update, delete, get results, pause, resume)
- Intent tools section in README

### Changed

- Article `content` field now documented as Markdown (raw HTML will not be rendered)
- Bumped plugin version to 1.1.0

## [1.0.0] - 2026-03-06

### Added

- Marketplace manifest (`.claude-plugin/marketplace.json`) for plugin discovery in Claude Desktop and Claude Code
- Claude Desktop / claude.ai install instructions (plugin via marketplace and connector-only)
- Skills-only install instructions via [vercel-labs/skills](https://github.com/vercel-labs/skills)

### Changed

- Fixed install commands to use correct repository name (`listing-labs/listing-plugin`)
- Fixed Claude Code install to use `/plugin marketplace add` and `/plugin install`
- Reorganized prerequisites section to clarify auth differences between Claude Code (API key) and Claude Desktop (OAuth)

### Fixed

- `.gitignore` now excludes `.idea/` directory