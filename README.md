# LibreChat MCP Server Configuration Guide

This repository contains the configuration for a LibreChat instance running at [alpha.guidelayer.ai](https://alpha.guidelayer.ai), which is one-click deployed on Railway. The configuration file (`librechat-up-l.yaml`) controls various aspects of LibreChat including model endpoints, search providers, and most importantly, **MCP (Model Context Protocol) servers**.

## What This Project Does

This project provides a LibreChat server configuration that enables:
- Multiple AI model endpoints (OpenAI, Claude, Mistral, Groq, etc.)
- Web search capabilities
- **MCP servers for enhanced functionality** (the main focus of this guide)

## MCP Server Setup Process

After extensive troubleshooting and development research, here's the complete workflow for adding MCP servers to LibreChat:

### 1. MCP Server Setup via Composio

**Important**: Use [mcp.composio.dev](https://mcp.composio.dev) - **NOT** the main Composio interface. The MCP-specific interface is much simpler and designed specifically for MCP connections.

**Prerequisites**: 
- Billing@guidelayer.com account at composio

### 2. Creating MCP Connections

1. Go to [mcp.composio.dev](https://mcp.composio.dev)
2. Navigate to the "All Apps" section
3. Find the application you want to connect to
4. Click "Create Server"
5. Complete the authentication process
6. **Important**: Select tools using the "Important Hints" options as defaults
7. Complete the server setup

### 3. Getting the Connection URL

After completing the server setup, you'll receive an HTTP connection URL. This URL will look something like:
```
https://apollo-3irns8zl6-composio.vercel.app/v3/mcp/[UUID]/mcp?include_composio_helper_actions=true
```

**Note**: The `?include_composio_helper_actions=true` parameter is crucial for proper functionality.

### 4. Adding MCP Servers to LibreChat Configuration

In the `librechat-up-l.yaml` file, add new MCP servers under the `mcpServers` section:

```yaml
mcpServers:
  # Example of a working Composio-based MCP server
  google calendar:
    type: streamable-http
    url: "https://apollo-3irns8zl6-composio.vercel.app/v3/mcp/620d32da-d8f9-4aeb-8873-7ff5b70b8447/mcp?include_composio_helper_actions=true"
  
  # Another example
  asana:
    type: streamable-http
    url: "https://apollo-3irns8zl6-composio.vercel.app/v3/mcp/a4978005-3fa5-4bdd-b7c5-6b939ddb6696/mcp?include_composio_helper_actions=true"
```

**Configuration Parameters**:
- `type`: Use `streamable-http` for Composio-based MCP servers
- `url`: The complete URL from Composio (including the `include_composio_helper_actions=true` parameter)
- `name`: Choose a descriptive name for the MCP server

### 5. Railway Deployment Trick

**Critical Step**: After updating the YAML configuration, you need to trigger a Railway redeployment:

1. Go to Railway dashboard
2. Navigate to the Variables section
3. Find the config path URL
4. **Add or remove a question mark (?) at the end of the URL**
5. Save the change
6. Deploy the changes

This "trick" forces Railway to recognize the configuration as changed and triggers a redeployment, even though the actual configuration hasn't changed.

### 6. Testing MCP Functionality

After deployment:
1. Wait for the Railway deployment to complete
2. Test the MCP server functionality in LibreChat
3. Verify that the MCP tools are available and working

## Current Working MCP Servers

Based on the configuration file, these MCP servers are currently functional:

### Composio-Based MCP Servers
- **Google Calendar**: `streamable-http` type with Composio URL
- **Asana**: `streamable-http` type with Composio URL

### Direct MCP Servers
- **Guidemap**: `sse` type with direct Railway deployment URL

## Configuration File Structure

The `librechat-up-l.yaml` file contains several key sections:

- **MCP Servers**: Configuration for all MCP connections
- **Endpoints**: AI model configurations (OpenAI, Claude, Mistral, etc.)
- **Web Search**: Search provider settings
- **Interface**: UI and privacy settings

## Troubleshooting Notes

### Common Issues and Solutions

1. **MCP Not Showing**: 
   - Ensure `cache: false` is set in the configuration
   - Verify the MCP server type is correct (`streamable-http` for Composio)

2. **Authentication Failures**:
   - Check that OAuth redirect URIs are properly configured
   - Verify API keys and tokens are correctly set in Railway environment variables

3. **Deployment Issues**:
   - Use the Railway deployment trick (adding/removing `?` from config URL)
   - Ensure all environment variables are properly set

### Working vs. Non-Working Configurations

**Working**:
- `type: streamable-http` with Composio URLs
- `type: sse` with direct Railway deployment URLs

**Not Working**:
- `type: sse` with some OAuth-based services (Asana, Notion in some cases)

## Environment Variables

The following environment variables are referenced in the configuration and should be set in Railway:

- `SERPER_API_KEY`
- `FIRECRAWL_API_KEY`
- `APIPIE_API_KEY`
- `COHERE_API_KEY`
- `FIREWORKS_API_KEY`
- `GROQ_API_KEY`
- `MISTRAL_API_KEY`
- `OPENROUTER_KEY`
- `PERPLEXITY_API_KEY`
- `SHUTTLEAI_API_KEY`
- `TOGETHERAI_API_KEY`

## Version History

- **Version 1.2.8** (8/29/25): Initial working MCP configuration
- Added working Composio-based MCP servers
- Configured direct Guidemap MCP server
- Resolved caching issues that prevented MCP from showing

## Support and Maintenance

For issues or questions regarding this configuration:
1. Check the troubleshooting section above
2. Verify all environment variables are set in Railway
3. Ensure the Railway deployment trick is used after configuration changes
4. Test MCP functionality after each deployment

---

**Last Updated**: August 30, 2025  
**Configuration Version**: 1.2.8  
**Deployment Platform**: Railway  
**LibreChat Instance**: [alpha.guidelayer.ai](https://alpha.guidelayer.ai)
