# Vite: dependency cache never invalidates with Yarn 4 `nodeLinker: pnpm`

Vite 8.3.3, Yarn 4.1.1. Run `corepack enable` once first (otherwise a global Yarn 1.x refuses to run because of the
`packageManager` field), or prefix every `yarn` command below with `corepack`.

1. `yarn install` — note that `node_modules/.yarn-state.yml` and `node_modules/.pnpm` do not exist.
2. `yarn dev`, open the page so `lodash-es` gets pre-bundled, stop the server.
3. Change `lodash-es` to `4.17.21` in `package.json`, run `yarn install`.
4. `yarn dev` again and open the page.

Expected: Vite logs "Re-optimizing dependencies because lockfile has changed" and re-bundles.

Actual: nothing is re-bundled. `lockfileHash` in `node_modules/.vite/deps/_metadata.json` is `e3b0c442` (sha256 of an empty
string) before and after, and the pre-bundled copy still contains `"4.17.20"`. `yarn dev --force` fixes it, but
`lockfileHash` stays `e3b0c442`.
