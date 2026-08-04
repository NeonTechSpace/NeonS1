# Imports and exports

- These rules apply to every package in this folder.
- Use a stable alias when code imports from another project file.
- Do not add imports that start with `./` or `../`.
- Use the package name when importing from another package, such as `@neons1/core`.
- Use a private `#...` alias when importing from the same package.
- Put each private alias in that package's `package.json` `imports` map.
- Make sure TypeScript, tests, and the built JavaScript can all use the alias.
- TypeScript `paths` alone are not enough because Node must also understand the built import.
- Use the same alias rule for statements like `export ... from`.
- This rule does not apply to paths used to read or write data files.
- Old relative imports may stay until the current task needs to change them.
- Do not rewrite all old imports unless the user asks for that work.
- If the needed alias does not exist, add the smallest working alias setup before adding the import or export.
