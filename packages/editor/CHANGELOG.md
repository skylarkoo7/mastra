# @mastra/editor

## 0.2.0-alpha.1

### Patch Changes

- Fix stored agents functionality and type consistency: ([#12672](https://github.com/mastra-ai/mastra/pull/12672))

  ## Server (`@mastra/server`)
  - Fixed auto-versioning bug where `activeVersionId` wasn't being updated when creating new versions
  - Added `GET /vectors` endpoint to list available vector stores
  - Added `GET /embedders` endpoint to list available embedding models
  - Added validation for memory configuration when semantic recall is enabled
  - Fixed version comparison in `handleAutoVersioning` to use the active version instead of latest
  - Added proper cache clearing after agent updates

  ## Client SDK (`@mastra/client-js`)
  - Updated `CreateStoredAgentParams` and `UpdateStoredAgentParams` types to match server schemas
  - Added proper `SerializedMemoryConfig` type with all fields including `embedder` and `embedderOptions`
  - Fixed `StoredAgentScorerConfig` to use correct sampling types (`'none' | 'ratio'`)
  - Added `listVectors()` and `listEmbedders()` methods to the client
  - Added corresponding `ListVectorsResponse` and `ListEmbeddersResponse` types

  ## Core (`@mastra/core`)
  - Updated `SerializedMemoryConfig` to allow `embedder?: EmbeddingModelId | string` for flexibility
  - Exported `EMBEDDING_MODELS` and `EmbeddingModelInfo` for use in server endpoints

  ## Editor (`@mastra/editor`)
  - Fixed memory persistence bug by handling missing vector store gracefully
  - When semantic recall is enabled but no vector store is configured, it now disables semantic recall instead of failing
  - Fixed type compatibility for `embedder` field when creating agents from stored config

  ## Playground UI (`@mastra/playground-ui`)
  - Fixed memory configuration in agent forms to use `SerializedMemoryConfig` object instead of string
  - Added `MemoryConfigurator` component for proper memory settings UI
  - Fixed scorer sampling configuration to remove unsupported 'count' option
  - Added `useVectors` and `useEmbedders` hooks to fetch available options from API
  - Fixed agent creation flow to use the server-returned agent ID for navigation
  - Fixed form validation schema to properly handle memory configuration object

- Updated dependencies [[`1ff1bb7`](https://github.com/mastra-ai/mastra/commit/1ff1bb7133ddcc14ce1822961efdd879b2ac5457)]:
  - @mastra/core@1.2.0-alpha.2

## 0.2.0-alpha.0

### Minor Changes

- Created @mastra/editor package for managing and resolving stored agent configurations ([#12631](https://github.com/mastra-ai/mastra/pull/12631))

  This major addition introduces the editor package, which provides a complete solution for storing, versioning, and instantiating agent configurations from a database. The editor seamlessly integrates with Mastra's storage layer to enable dynamic agent management.

  **Key Features:**
  - **Agent Storage & Retrieval**: Store complete agent configurations including instructions, model settings, tools, workflows, nested agents, scorers, processors, and memory configuration
  - **Version Management**: Create and manage multiple versions of agents, with support for activating specific versions
  - **Dependency Resolution**: Automatically resolves and instantiates all agent dependencies (tools, workflows, sub-agents, etc.) from the Mastra registry
  - **Caching**: Built-in caching for improved performance when repeatedly accessing stored agents
  - **Type Safety**: Full TypeScript support with proper typing for stored configurations

  **Usage Example:**

  ```typescript
  import { MastraEditor } from '@mastra/editor';
  import { Mastra } from '@mastra/core';

  // Initialize editor with Mastra
  const mastra = new Mastra({
    /* config */
    editor: new MastraEditor(),
  });

  // Store an agent configuration
  const agentId = await mastra.storage.stores?.agents?.createAgent({
    name: 'customer-support',
    instructions: 'Help customers with inquiries',
    model: { provider: 'openai', name: 'gpt-4' },
    tools: ['search-kb', 'create-ticket'],
    workflows: ['escalation-flow'],
    memory: { vector: 'pinecone-db' },
  });

  // Retrieve and use the stored agent
  const agent = await mastra.getEditor()?.getStoredAgentById(agentId);
  const response = await agent?.generate('How do I reset my password?');

  // List all stored agents
  const agents = await mastra.getEditor()?.listStoredAgents({ pageSize: 10 });
  ```

  **Storage Improvements:**
  - Fixed JSONB handling in LibSQL, PostgreSQL, and MongoDB adapters
  - Improved agent resolution queries to properly merge version data
  - Enhanced type safety for serialized configurations

### Patch Changes

- Updated dependencies [[`2770921`](https://github.com/mastra-ai/mastra/commit/2770921eec4d55a36b278d15c3a83f694e462ee5), [`b1695db`](https://github.com/mastra-ai/mastra/commit/b1695db2d7be0c329d499619c7881899649188d0), [`4133d48`](https://github.com/mastra-ai/mastra/commit/4133d48eaa354cdb45920dc6265732ffbc96788d), [`5dd01cc`](https://github.com/mastra-ai/mastra/commit/5dd01cce68d61874aa3ecbd91ee17884cfd5aca2), [`13e0a2a`](https://github.com/mastra-ai/mastra/commit/13e0a2a2bcec01ff4d701274b3727d5e907a6a01), [`c987384`](https://github.com/mastra-ai/mastra/commit/c987384d6c8ca844a9701d7778f09f5a88da7f9f), [`cb8cc12`](https://github.com/mastra-ai/mastra/commit/cb8cc12bfadd526aa95a01125076f1da44e4afa7), [`62f5d50`](https://github.com/mastra-ai/mastra/commit/62f5d5043debbba497dacb7ab008fe86b38b8de3)]:
  - @mastra/memory@1.1.0-alpha.1
  - @mastra/core@1.2.0-alpha.1

## 0.1.0

### Minor Changes

- Initial release of @mastra/editor
  - Agent storage and retrieval from database
  - Dynamic agent creation from stored configurations
  - Support for tools, workflows, nested agents, memory, and scorers
  - Integration with Mastra core for seamless agent management
