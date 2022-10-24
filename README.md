# action-eligible-actor

> Execute a GitHub Action only if the (triggering) actor is eligible (=authorised) to do so.

## Use Case

Assume you have multiple workflows and a complex set of rules who can manually
trigger which workflow. Instead of adding `if` conditions here and there you can
define a set of rules (in `eligible-actors-rules.json`) and use this rule in
multiple places. The management of the rules is centralized in one place.

But even if you only have a `deployment.yml` or `release.yml` workflow, this
action can be very useful.

## Usage

Instead of adding `if` conditions to individual workflows and steps, use this
action to standardize and easily manage which actor can perform which workflow.
Simply add the action to your workflow, define `rulesFile` and the `ruleId` to
apply to and decide if the workflow should fail silently (`failSilently`) or
not.

### Before

```yml
- name: Release Tag
  if: ${{ github.actor == 'username' }}
  run: npx semantic-release
```

### After

```yml
- name: Can actor release?
  uses: natterstefan/action-eligible-actor@v1
  with:
    rulesFile: 'eligible-actors-rules.json'
    ruleId: 1

# if `failSilently` for the rule with the id `1` was set to `false`, this step
# will not start if the actor is not eligible (included in `eligibleActors`).
# Instead the workflow will exit with 1 (=failure).
- name: Release Tag
  run: npx semantic-release
```

With the following `eligible-actors-rules.json`:

```json
[
  {
    "id": "1",
    "description": "Only repo owner and act",
    "eligibleActors": ["natterstefan"],
    "failureMessage": "Only the repo owner can do this!",
    "failSilently": false
  }
]
```

Take a look at more examples in the
[`test.yml`](.github/workflows/test.yml#L24) Workflow file.

## Development

> First, you'll need to have a reasonably modern version of `node` handy. This
> won't work with versions older than 16, for instance.

Install the dependencies

```bash
npm install
```

Build the package for distribution

```bash
# package the source files
npm run package
# afterward create a release with the release GitHub action
```

Run the tests

```bash
npm run package # or npm run dev (watch mode)
npm test
```

Test the workflow locally with <https://github.com/nektos/act>!

```bash
npm run package # or npm run dev (watch mode)
act -j testFailSilentyTrue && act -j testFailSilentyFalse
```

### LICENSE

[MIT](LICENSE)

## Contributors ✨

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tr>
    <td align="center"><a href="https://natterstefan.me/"><img src="https://avatars.githubusercontent.com/u/1043668?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Stefan Natter</b></sub></a><br /><a href="#ideas-natterstefan" title="Ideas, Planning, & Feedback">🤔</a> <a href="https://github.com/natterstefan/action-eligible-actor/commits?author=natterstefan" title="Code">💻</a> <a href="https://github.com/natterstefan/action-eligible-actor/commits?author=natterstefan" title="Documentation">📖</a></td>
  </tr>
</table>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!
