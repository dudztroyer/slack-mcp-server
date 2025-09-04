# slack-mcp-server

[![Build Status](https://github.com/dudztroyer/slack-mcp-server/workflows/Test/badge.svg)](https://github.com/dudztroyer/slack-mcp-server/actions)
[![npm version](https://badge.fury.io/js/%40dudztroyer%2Fslack-mcp-server.svg)](https://www.npmjs.com/package/@dudztroyer/slack-mcp-server)

## Disclaimer

This project includes [code](https://github.com/modelcontextprotocol/servers-archived/tree/main/src/slack) originally developed by Anthropic and released under the MIT License. Substantial modifications and new functionality have been added by dudztroyer, and are licensed under the Apache License, Version 2.0.

## Overview

A Model Context Protocol (MCP) server for interacting with Slack workspaces. This server provides tools to list channels, post messages (with optional markdown formatting), reply to threads (with optional markdown formatting), add reactions, get channel history, get thread replies, and manage users.

## Available Tools

1. **slack_list_channels**

   - List public or pre-defined channels in the workspace
   - Optional inputs:
     - `limit` (number, default: 100, max: 200): Maximum number of channels to return
     - `cursor` (string): Pagination cursor for next page
   - Returns: List of channels with their IDs and information

2. **slack_post_message**

   - Post a new message to a Slack channel
   - Required inputs:
     - `channel_id` (string): The ID of the channel to post to
     - `text` (string): The message text to post
   - Returns: Message posting confirmation and timestamp

3. **slack_reply_to_thread**

   - Reply to a specific message thread
   - Required inputs:
     - `channel_id` (string): The channel containing the thread
     - `thread_ts` (string): Timestamp of the parent message
     - `text` (string): The reply text
   - Returns: Reply confirmation and timestamp

4. **slack_add_reaction**

   - Add an emoji reaction to a message
   - Required inputs:
     - `channel_id` (string): The channel containing the message
     - `timestamp` (string): Message timestamp to react to
     - `reaction` (string): Emoji name without colons
   - Returns: Reaction confirmation

5. **slack_get_channel_history**

   - Get recent messages from a channel
   - Required inputs:
     - `channel_id` (string): The channel ID
   - Optional inputs:
     - `limit` (number, default: 10): Number of messages to retrieve
   - Returns: List of messages with their content and metadata

6. **slack_get_thread_replies**

   - Get all replies in a message thread
   - Required inputs:
     - `channel_id` (string): The channel containing the thread
     - `thread_ts` (string): Timestamp of the parent message
   - Returns: List of replies with their content and metadata

7. **slack_get_users**

   - Get list of workspace users with basic profile information
   - Optional inputs:
     - `cursor` (string): Pagination cursor for next page
     - `limit` (number, default: 100, max: 200): Maximum users to return
   - Returns: List of users with their basic profiles

8. **slack_get_user_profile**

   - Get detailed profile information for a specific user
   - Required inputs:
     - `user_id` (string): The user's ID
   - Returns: Detailed user profile information

9. **slack_post_markdown_message**

   - Post a new message to a Slack channel with markdown formatting enabled
   - Required inputs:
     - `channel_id` (string): The ID of the channel or user to post to
     - `text` (string): The message text with markdown formatting (mrkdwn). Supports _bold_, _italic_, `code`, `code blocks`, >quotes, <@user_id> mentions, <#channel_id> channel links, and <url|link text> links
   - Returns: Message posting confirmation and timestamp

10. **slack_reply_to_thread_markdown**
    - Reply to a specific message thread in Slack with markdown formatting enabled
    - Required inputs:
      - `channel_id` (string): The ID of the channel containing the thread
      - `thread_ts` (string): The timestamp of the parent message in the format '1234567890.123456'. Timestamps in the format without the period can be converted by adding the period such that 6 numbers come after it
      - `text` (string): The reply text with markdown formatting (mrkdwn). Supports _bold_, _italic_, `code`, `code blocks`, >quotes, <@user_id> mentions, <#channel_id> channel links, and <url|link text> links
    - Returns: Reply confirmation and timestamp

## Slack Bot Setup

To use this MCP server, you need to create a Slack app and configure it with the necessary permissions:

### 1. Create a Slack App

- Visit the [Slack Apps page](https://api.slack.com/apps)
- Click "Create New App"
- Choose "From scratch"
- Name your app and select your workspace

### 2. Configure Bot Token Scopes

Navigate to "OAuth & Permissions" and add these scopes:

- `channels:history` - View messages and other content in public channels
- `channels:read` - View basic channel information
- `chat:write` - Send messages as the app
- `reactions:write` - Add emoji reactions to messages
- `users:read` - View users and their basic information
- `users.profile:read` - View detailed profiles about users

### 3. Install App to Workspace

- Click "Install to Workspace" and authorize the app
- Save the "Bot User OAuth Token" that starts with `xoxb-`

### 4. Get Your Team ID

Get your Team ID (starts with a `T`) by following [this guidance](https://slack.com/help/articles/221769328-Locate-your-Slack-URL-or-ID#find-your-workspace-or-org-id)

### 5. Add Bot to Channels (Optional)

For the bot to access private channels or to post messages, you may need to invite it to specific channels using `/invite @your-bot-name`

## Features

- **Multiple Transport Support**: Supports both stdio and Streamable HTTP transports
- **Modern MCP SDK**: Updated to use the latest MCP SDK (v1.13.2) with modern APIs
- **Comprehensive Slack Integration**: Full set of Slack operations including:
  - List channels (with predefined channel support)
  - Post messages (plain text and markdown formatting)
  - Reply to threads (plain text and markdown formatting)
  - Add reactions
  - Get channel history
  - Get thread replies
  - List users
  - Get user profiles

## Installation

### Local Development

```bash
npm install
npm run build
```

### Global Installation (NPM)

```bash
npm install -g dudztroyer/slack-mcp-server
```

## Configuration

Set the following environment variables:

```bash
export SLACK_BOT_TOKEN="xoxb-your-bot-token"
export SLACK_TEAM_ID="your-team-id"
export SLACK_CHANNEL_IDS="channel1,channel2,channel3"  # Optional: predefined channels
export AUTH_TOKEN="your-auth-token"  # Optional: Bearer token for HTTP authorization (Streamable HTTP transport only)
```

## Usage

### Command Line Options

```bash
slack-mcp [options]

Options:
  --transport <type>     Transport type: 'stdio' or 'http' (default: stdio)
  --port <number>        Port for HTTP server when using Streamable HTTP transport (default: 3000)
  --token <token>        Bearer token for HTTP authorization (optional, can also use AUTH_TOKEN env var)
  --help, -h             Show this help message
```

### Local Usage Examples

#### Using the slack-mcp command (after global installation)

```bash
# Use stdio transport (default)
slack-mcp

# Use stdio transport explicitly
slack-mcp --transport stdio

# Use Streamable HTTP transport on default port 3000
slack-mcp --transport http

# Use Streamable HTTP transport on custom port
slack-mcp --transport http --port 8080

# Use Streamable HTTP transport with custom auth token
slack-mcp --transport http --token mytoken

# Use Streamable HTTP transport with auth token from environment variable
AUTH_TOKEN=mytoken slack-mcp --transport http
```

#### Using node directly (for development)

```bash
# Use stdio transport (default)
node dist/index.js

# Use stdio transport explicitly
node dist/index.js --transport stdio

# Use Streamable HTTP transport on default port 3000
node dist/index.js --transport http

# Use Streamable HTTP transport on custom port
node dist/index.js --transport http --port 8080

# Use Streamable HTTP transport with custom auth token
node dist/index.js --transport http --token mytoken

# Use Streamable HTTP transport with auth token from environment variable
AUTH_TOKEN=mytoken node dist/index.js --transport http
```

## Transport Types

### Stdio Transport

- **Use case**: Command-line tools and direct integrations
- **Communication**: Standard input/output streams
- **Default**: Yes

### Streamable HTTP Transport

- **Use case**: Remote servers and web-based integrations
- **Communication**: HTTP POST requests with optional Server-Sent Events streams
- **Features**:
  - Session management
  - Bidirectional communication
  - Resumable connections
  - RESTful API endpoints
  - Bearer token authentication

## Authentication (Streamable HTTP Transport Only)

When using Streamable HTTP transport, the server supports Bearer token authentication:

1. **Command Line**: Use `--token <token>` to specify a custom token
2. **Environment Variable**: Set `AUTH_TOKEN=<token>` as a fallback
3. **Auto-generated**: If neither is provided, a random token is generated

The command line option takes precedence over the environment variable. Include the token in HTTP requests using the `Authorization: Bearer <token>` header.

## Troubleshooting

If you encounter permission errors, verify that:

1. All required scopes are added to your Slack app
2. The app is properly installed to your workspace
3. The tokens and workspace ID are correctly copied to your configuration
4. The app has been added to the channels it needs to access

## Development

### Build

```bash
npm run build
```

### Watch Mode

```bash
npm run watch
```

### Creating Releases

This project includes an automated release workflow that can be triggered manually via GitHub Actions:

#### How to Create a Release

1. **Navigate to GitHub Actions**: Go to the "Actions" tab in your GitHub repository
2. **Select Release Workflow**: Click on "Create Release" workflow
3. **Run Workflow**: Click "Run workflow" button
4. **Configure Release**:
   - **Version bump type**: Choose from `patch`, `minor`, or `major`

#### What the Release Workflow Does

The automated release process leverages [changelogen](https://github.com/unjs/changelogen) to handle most of the release process:

1. **Quality Checks**: Ensures all tests pass and code builds successfully
2. **Changelogen Release**: Uses `changelogen --release --push` and `changelogen gh release` to:
   - Generate beautiful changelog from Conventional Commits
   - Bump version in `package.json` according to your selection
   - Create and push git tag
   - Commit and push all changes
   - Create GitHub release with changelog as release notes
3. **Distribution & Assets**: Adds distribution archive to the GitHub release
4. **Trigger Downstream**: Automatically triggers:
   - Package publishing to GitHub Package Registry (`publish.yml`)

#### Version Bump Types

- **Patch** (`1.0.0` → `1.0.1`): Bug fixes and minor changes
- **Minor** (`1.0.0` → `1.1.0`): New features that don't break existing functionality
- **Major** (`1.0.0` → `2.0.0`): Breaking changes

#### Conventional Commits for Better Changelogs

The release workflow uses [changelogen](https://github.com/unjs/changelogen) which works best with [Conventional Commits](https://www.conventionalcommits.org/). To get the most beautiful and informative changelogs, format your commit messages like:

- `feat: add new slack reaction tool` - New features
- `fix: resolve channel history pagination issue` - Bug fixes
- `docs: update README with Docker instructions` - Documentation changes
- `chore: update dependencies` - Maintenance tasks
- `refactor: simplify error handling logic` - Code refactoring
- `perf: optimize message processing` - Performance improvements

This will result in properly categorized and formatted changelog entries.

## API Endpoints (Streamable HTTP Transport)

When using Streamable HTTP transport, the server exposes the following endpoints:

- `POST /mcp` - Client-to-server communication
- `GET /mcp` - Server-to-client notifications (Server-Sent Events streams)
- `DELETE /mcp` - Session termination

## Changes from Previous Version

- **Updated MCP SDK**: Upgraded from v1.0.1 to v1.13.2
- **Modern API**: Migrated from low-level Server class to high-level McpServer class
- **Zod Validation**: Added proper schema validation using Zod
- **Transport Flexibility**: Added support for Streamable HTTP transport
- **Command Line Interface**: Added CLI arguments for transport selection
- **Session Management**: Implemented proper session handling for HTTP transport
- **Better Error Handling**: Improved error handling and logging
