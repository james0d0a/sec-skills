# Ghidra MCP capability map

What the `/reverse-engineer` skill needs from a Ghidra MCP server, and the tool that provides each on the server it was written against (GhidraMCP, `bridge_mcp_ghidra.py`, release 1.4). Other servers expose the same capabilities under other names: list the server's tools first and fill in the right-hand column before relying on it.

Every call below runs against the program currently open in Ghidra's CodeBrowser. Confirm it is the right program before trusting a single result.

| Need | Capability | GhidraMCP 1.4 tool |
|---|---|---|
| Phase 0 check | Prove the server is live against the right program | `list_segments`, `get_current_function` |
| Triage | Segments and permissions | `list_segments` |
| Triage | Imports, paged to the end | `list_imports` |
| Triage | Exports | `list_exports` |
| Triage | Strings, with a filter | `list_strings` |
| Triage | Function census | `list_functions`, `list_methods` |
| Triage | Defined data and globals | `list_data_items` |
| Triage | Namespaces and classes (C++ targets) | `list_namespaces`, `list_classes` |
| Surface | Find a function by name fragment | `search_functions_by_name` |
| Surface | Function at an address | `get_function_by_address` |
| Trace | Decompile | `decompile_function`, `decompile_function_by_address` |
| Trace | Disassemble when the decompiler is suspect | `disassemble_function` |
| Trace | Callers of an address or function | `get_xrefs_to`, `get_function_xrefs` |
| Trace | Callees and data references from a function | `get_xrefs_from` |
| Label | Rename a function | `rename_function`, `rename_function_by_address` |
| Label | Rename a global or data item | `rename_data` |
| Label | Rename a local or parameter | `rename_variable` |
| Label | Plate and inline comments | `set_decompiler_comment`, `set_disassembly_comment` |
| Fix | Set the prototype | `set_function_prototype` |
| Fix | Retype a local | `set_local_variable_type` |
| Navigate | Where the analyst is looking | `get_current_address`, `get_current_function` |

## Gaps on this server, and what to do about them

- **No struct definition tool.** Recover the layout and record it in the plate comment and the report; ask the user to define the type in Ghidra, or do it through a script if the server grows one.
- **No hashing, entropy, or unpacking.** Shell: `shasum -a 256`, `file`, an entropy script, `binwalk`. These touch the sample as a file only; nothing executes.
- **No program switching.** The user opens the program. If the census looks wrong for the sample named, stop and confirm which program is open.
- **Paged lists.** Every list tool takes `offset` and `limit`. A list read to one page is not a list read; page until the result is short.
- **Decompiler cache.** After a prototype or type change, decompile again before reading; the prior output is stale.
