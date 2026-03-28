---
name: camel-route-developer
description: Develops Apache Camel integration routes in YAML DSL compatible with Kaoto Visual Editor (.camel.yaml files). Use when user says "create a Camel route", "build an integration", or works with Camel components, EIPs, or Kamelets. Uses the Camel MCP server for all catalog lookups and route validation. Never searches the web. Supports running routes with Camel CLI and JBang.
---

# Camel Route Developer

Develop Apache Camel integration routes using YAML DSL, the Camel MCP server for documentation, and Camel CLI (with JBang) for running routes. All Camel routes must be compatible with the Kaoto Visual Editor.

## Critical Rules

- **NEVER search the web** for Camel documentation. Always use the Camel MCP server tools.
- **NEVER guess the Camel version.** Always detect it first by running `camel --version` before making any Camel MCP server calls. Use the detected version as the `camelVersion` parameter for all MCP tool calls.
- **ALL route files MUST end with `.camel.yaml`** (e.g., `my-route.camel.yaml`) so they are recognized by the Kaoto Visual Editor.
- **Always use YAML DSL** for route definitions. Do not use XML or Java DSL unless explicitly asked.
- **Always validate routes** before delivering them to the user.

## Instructions

### Step 1: Detect the Camel Version

Before doing anything else, run the following command to detect the installed Camel CLI version:

```bash
camel --version
```

Parse the version number from the output (e.g., `4.18.0`). This version MUST be used as the `camelVersion` parameter in all subsequent Camel MCP server tool calls. Never hardcode, assume, or guess the version.

If the command fails (Camel CLI not installed), ask the user to install it or to provide the target Camel version manually.

### Step 2: Understand the Integration Requirement

Clarify what the user wants to integrate:
- Source and target systems (e.g., file, kafka, http, database)
- Data transformations needed
- Error handling requirements
- Any specific EIPs (Enterprise Integration Patterns) to use

### Step 3: Look Up Components and EIPs via the Camel MCP Server

Use the Camel MCP server tools to gather documentation. **Do not guess component options.**

- **Find components:** Use `mcp__camel__camel_catalog_components` to search for available components by name or category.
- **Get component details:** Use `mcp__camel__camel_catalog_component_doc` to retrieve all endpoint parameters and options for a specific component.
- **Find EIPs:** Use `mcp__camel__camel_catalog_eips` to search for patterns (routing, transformation, error handling).
- **Get EIP details:** Use `mcp__camel__camel_catalog_eip_doc` for detailed usage of a specific EIP.
- **Find Kamelets:** Use `mcp__camel__camel_catalog_kamelets` to search for ready-made connectors (source, sink, action).
- **Get Kamelet details:** Use `mcp__camel__camel_catalog_kamelet_doc` for properties and usage.
- **Data formats:** Use `mcp__camel__camel_catalog_dataformats` and `mcp__camel__camel_catalog_dataformat_doc` for marshalling/unmarshalling options.
- **Expression languages:** Use `mcp__camel__camel_catalog_languages` and `mcp__camel__camel_catalog_language_doc` for simple, jsonpath, xpath, jq, etc.
- **Check Camel versions:** Use `mcp__camel__camel_version_list` when the user needs version-specific information.

### Step 4: Write the Route in YAML DSL

Create the route file with the `.camel.yaml` extension. Follow this structure:

```yaml
- route:
    id: my-route-id
    from:
      uri: "component:endpoint"
      parameters:
        key: value
      steps:
        - to:
            uri: "component:endpoint"
```

Key guidelines:
- Use descriptive route IDs
- Set component parameters explicitly (look them up, do not guess)
- Use `simple` language for expressions when possible
- Add `log` steps for observability
- Add error handling with `onException` or `errorHandler` when appropriate

### Step 5: Validate the Route

Before delivering the route to the user, validate it:

1. **Validate YAML DSL structure:** Use `mcp__camel__camel_validate_yaml_dsl` to check the route conforms to the Camel YAML DSL schema.
2. **Validate endpoint URIs:** Use `mcp__camel__camel_validate_route` to check individual endpoint URIs for correct syntax and valid parameters.
3. **Get route context:** Use `mcp__camel__camel_route_context` to verify all components and EIPs are correctly referenced.

Fix any validation errors before presenting the route.

### Step 6: Security Review (When Applicable)

For routes that handle sensitive data or connect to external systems:

- Use `mcp__camel__camel_route_harden_context` to get security recommendations.
- Apply suggested hardening measures (TLS, authentication, input validation).

### Step 7: Provide Run Instructions

Tell the user how to run the route with Camel CLI and JBang:

```bash
# Install Camel CLI via JBang (one-time setup)
jbang app install camel@apache/camel

# Run the route
camel run my-route.camel.yaml

# Run with live reload (development mode)
camel run my-route.camel.yaml --dev

# Run with a specific Camel version
camel run my-route.camel.yaml --camel-version=4.10.0

# Export as a standalone project (Spring Boot or Quarkus)
camel export my-route.camel.yaml --runtime=spring-boot --directory=my-project
```

### Step 8: Transform Routes (When Asked)

If the user needs to convert between DSL formats:
- Use `mcp__camel__camel_transform_route` to convert between YAML, XML, and Java DSL.
- Note: Java-to-YAML/XML has limitations.

## Examples

### Example 1: Simple File-to-Kafka Route
User says: "Create a Camel route that reads CSV files from a directory and sends them to Kafka"
Actions:
1. Look up `file` and `kafka` component docs via MCP
2. Look up `csv` data format doc via MCP
3. Write `file-to-kafka.camel.yaml` with file consumer, CSV unmarshal, and Kafka producer
4. Validate the route with MCP tools
5. Provide `camel run file-to-kafka.camel.yaml` instructions
Result: A validated `.camel.yaml` file ready to open in Kaoto or run with Camel CLI

### Example 2: REST API with Content-Based Routing
User says: "Build an integration that exposes a REST endpoint and routes requests based on content"
Actions:
1. Look up `rest`, `direct` component docs and `choice` EIP doc via MCP
2. Write `rest-router.camel.yaml` with REST DSL and choice EIP
3. Validate and deliver
Result: A REST-enabled route with content-based routing in `.camel.yaml` format

### Example 3: Using a Kamelet
User says: "I want to receive messages from AWS S3 and log them"
Actions:
1. Search kamelets for "s3" via MCP
2. Get `aws-s3-source` kamelet doc via MCP
3. Write `s3-logger.camel.yaml` using the kamelet as source
4. Validate and deliver

## Troubleshooting

### Error: Route validation fails
Cause: Incorrect component parameters or YAML structure
Solution: Re-check component docs via `mcp__camel__camel_catalog_component_doc` and fix parameter names/values. Validate again with `mcp__camel__camel_validate_yaml_dsl`.

### Error: Camel CLI not found
Cause: JBang or Camel CLI not installed
Solution: Run `jbang app install camel@apache/camel`. If JBang itself is missing, install it first: `curl -Ls https://sh.jbang.dev | bash -s - app setup`

### Error: Route file not visible in Kaoto
Cause: File does not end with `.camel.yaml`
Solution: Rename the file to use the `.camel.yaml` extension (e.g., `my-route.camel.yaml`).
