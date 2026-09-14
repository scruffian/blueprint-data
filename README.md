# blueprint-data

[Blueprints](https://wordpress.github.io/wordpress-playground/blueprints) for WordPress Playground, with the WXR content they import. Each one boots a small, realistic site, so a tester opens a working shop or portfolio instead of a blank install.

## What's here

| Directory | Site | Theme |
|---|---|---|
| `coaching/` | AB Career Coaching | Twenty Twenty-Four |
| `cupcakes/` | Sally's Cupcakes | Twenty Twenty-Five |
| `photographer/` | Elliot Grey, a photography portfolio | Twenty Twenty-Five |

Every directory holds two blueprints. `blueprint.json` installs the theme, imports the content, and drops you in wp-admin.

`blueprint-cnl.json` builds the same site for the Create not Learn prototype. It also switches on the Gutenberg `gutenberg-extensible-site-editor` and `gutenberg-omnibar` experiments and lands on the v2 editor at `/wp-admin/admin.php?page=site-editor-v2`.

Both import the same WXR files, so the test content stays in sync.

## Running a standard blueprint

Pass the raw URL of the blueprint to Playground as `blueprint-url`.

```
https://playground.wordpress.net/?blueprint-url=https%3A%2F%2Fraw.githubusercontent.com%2Fscruffian%2Fblueprint-data%2Frefs%2Fheads%2Ftrunk%2Fcupcakes%2Fblueprint.json
```

To run it against current Gutenberg trunk rather than the released plugin, add `gutenberg-branch`.

```
https://playground.wordpress.net/?blueprint-url=https%3A%2F%2Fraw.githubusercontent.com%2Fscruffian%2Fblueprint-data%2Frefs%2Fheads%2Ftrunk%2Fcupcakes%2Fblueprint.json&gutenberg-branch=trunk
```

Swap `cupcakes` for `coaching` or `photographer` in any of these URLs.

## Running the Create not Learn prototype

The prototype lives in [Gutenberg PR #79549](https://github.com/WordPress/gutenberg/pull/79549), so the URL needs `gutenberg-pr=79549` alongside the `-cnl` blueprint.

### Sally's Cupcakes

```
https://playground.wordpress.net/?blueprint-url=https%3A%2F%2Fraw.githubusercontent.com%2Fscruffian%2Fblueprint-data%2Frefs%2Fheads%2Ftrunk%2Fcupcakes%2Fblueprint-cnl.json&gutenberg-pr=79549
```

### AB Career Coaching

```
https://playground.wordpress.net/?blueprint-url=https%3A%2F%2Fraw.githubusercontent.com%2Fscruffian%2Fblueprint-data%2Frefs%2Fheads%2Ftrunk%2Fcoaching%2Fblueprint-cnl.json&gutenberg-pr=79549
```

### Elliot Grey

```
https://playground.wordpress.net/?blueprint-url=https%3A%2F%2Fraw.githubusercontent.com%2Fscruffian%2Fblueprint-data%2Frefs%2Fheads%2Ftrunk%2Fphotographer%2Fblueprint-cnl.json&gutenberg-pr=79549
```

### Why these are pinned to WordPress 7.0

PR #79549 was last built on 26 June 2026. Installing that build on WordPress 7.1, which is now `latest`, fails during plugin activation with `Could not install plugin` and a PHP exit code of 255.

The `-cnl` blueprints therefore set `preferredVersions.wp` to `7.0`. Once the PR is rebased, drop the pin and they can follow `latest` again.

If you hit the same error on a blueprint that isn't pinned, add `&wp=7.0` to the URL as a one-off.

## Writing your own variant

Two changes turn a standard blueprint into a prototype one. First, set the landing page.

```json
"landingPage": "/wp-admin/admin.php?page=site-editor-v2"
```

Second, enable the experiments in a `runPHP` step placed last, so nothing earlier in the blueprint can overwrite the option.

```json
{
  "step": "runPHP",
  "code": "<?php require '/wordpress/wp-load.php'; update_option( 'gutenberg-experiments', array( 'gutenberg-extensible-site-editor' => true, 'gutenberg-omnibar' => true ) );"
}
```

## Notes on Playground URL parameters

Blueprints have no inheritance. There is no `extends` or `include` in either the v1 or v2 [schema](https://playground.wordpress.net/blueprint-schema.json), which is why the prototype variants are copies rather than wrappers.

Only some query parameters merge into a blueprint loaded via `blueprint-url`. Those are `wp`, `php`, `networking`, `language`, `multisite`, `login`, `url`, `core-pr`, `gutenberg-pr` and `gutenberg-branch`. Notably `url` overrides `landingPage`, which is handy for pointing an existing blueprint at a different screen.

`plugin`, `theme`, `import-wxr` and `import-site` are ignored when `blueprint-url` is present. They only apply to URLs that carry no blueprint at all.
