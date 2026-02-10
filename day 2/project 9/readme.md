# P9 – AI Agent with MCP

This project contains **two workflows** that together demonstrate how to expose n8n functionality as **MCP tools** and use them from an AI agent.

* MCP Server → exposes ticket operations as tools
* MCP Client → AI chatbot that uses those tools

## Part 1: Creating the MCP Server
**Purpose:** Serve a collection of tools via MCP for easy tool access, maintenance, and shareability.

### 1. Create New Workflow
- n8n home --> New workflow
- Name the workflow `P9 - MCP Server`

### 2. Add `MCP Server Trigger` node
- **Authentication**: None

### 3. Add custom tools
- Open a new tab and navigate to your previous workflow **P8 - Advanced AI Agent**
- **Select** and **Copy** the following tools:
  - **Update Ticket**
  - **Get Ticket**
  - **Create Ticket**
 (Right click --> **Copy 3 Nodes**
- Back to your `P9 - MCP Server` workflow
- Paste the tools
- **Connect the tools** to the MCP Server Node

### 4. Publish your MCP Server
- Hit Publish
- Open the **MCP Server** Node and **copy the production URL**

## Part 2: Creating the MCP Client
**Purpose:** Access our MCP server from our AI Agent.

### 1. Duplicate or Copy Workflow
- n8n home --> `P8 - Advanced Agent` --> Duplicate
- Alternative: [Import the workflow from here](https://github.com/tobiaszwingmann/n8n-ai-bootcamp/blob/main/day%202/project%208/n8n/P8%20%E2%80%93%20Advanced%20Agent.json).
- Name: `P9 - MCP Client`

### 2. Open the workflow `P9 - MCP Client`
- **Delete the following tools:**
  - **Update Ticket**
  - **Get Ticket**
  - **Create Ticket**

### 3. Add the MCP Client
- Click the `+` icon under Agent Tool
- Select **MCP Client Tool**
Parameters:
- **Endpoint:** The **Production URL** from `P9 - MCP Server`
- **Tools to Include**: `Selected`
- Confirm the tools are visible. If not, double check you published your server and pasted the correct URL.
- See the tools? **Select all**
http://localhost:5678/mcp/edd5ffc1-e861-4354-85e7-e95ce9195c93

### 4. Try the connection
- Ask your AI Agent to retrieve a ticket ID that is available on Github, e.g. `MLFNSMXT`
- Works? Your MCP Server is up and running
To-Do:
- Try creating a new Tickets or updating existing tickets

