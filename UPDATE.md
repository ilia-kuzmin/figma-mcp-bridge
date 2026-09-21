# Fork workflow (ilia-kuzmin/figma-mcp-bridge)

Remotes: `origin` = this fork, `upstream` = gethopp/figma-mcp-bridge.
Local deploy: MCP server runs `node ~/figma-mcp-bridge-src/server/dist/index.js`;
the Figma dev plugin reads `~/figma-mcp-bridge/dist/{code.js,index.html}`.

## Pull upstream changes

```bash
cd ~/figma-mcp-bridge-src
git fetch upstream
git rebase upstream/main          # our commits stay on top
git push --force-with-lease origin main
```

## Rebuild + redeploy after any change

```bash
(cd plugin && bun install && bun run build)
(cd server && bun install && bun run build)
cp plugin/dist/code.js plugin/dist/index.html ~/figma-mcp-bridge/dist/
```

Then re-run the plugin in Figma (Plugins → Development → Figma MCP Bridge)
and restart the Claude session so the MCP tool list is refreshed.

## Gotcha: adding variants to an existing component set

`reparent_nodes` of COMPONENT children from one COMPONENT_SET into another
works, but Figma rewrites the moved variants' names by inserting the *source
set's name* as the first property value and shifting the rest
(`Size=<set name>, Learned=Medium, Level=No, Property 4=0`). The target set is
then in an error state until every moved variant is renamed back to
`Prop=Value, …` form. An emptied source set is deleted by Figma automatically.
