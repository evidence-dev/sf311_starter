# SF 311 Data App Template

This is a template repo for building an interactive data app with [Evidence](https://evidence.dev) using SF 311 data. This project is part of a workshop at [Small Data SF 2024](https://www.smalldatasf.com/2024/).

Please read this full readme before starting.

## Getting Started

We recommend using VS Code and installing the Evidence extension.

You can get started by clicking the "Use this template" button at the top right of the page and creating a new repository. **Make sure you check the box to include all branches.**

You will see some banners in Github prompting you to open a PR for the other branches - ignore these.

Once you have the repo cloned locally, check out the `pages/index.md` file for instructions on what to build next.

## Template Contents

This template repo includes a DuckDB dataset containing 100k SF 311 records, as well as all required connection setup in Evidence. You don't need to do anything to connect the data source to the Evidence project.

## Important Note: geoJSON File

We have hosted a geoJSON file for mapping SF neighborhoods. You will need the URL for this file when creating an AreaMap:
https://evd-geojson.b-cdn.net/sf_hoods.geojson

## Sections

We've broken up the proejct into 6 sections:

1. Build a basic summary page
2. Build a simple categories page
3. Add interactivity to your summary page
4. Build a neighborhood explorer page
5. Create neighborhood templated page
6. Dynamic content generation - volume spikes

At the end of the project, you'll have a fast, fully interactive data app that looks great on desktop, laptop, or mobile devices.

## Branches

You should get started on the `main` branch of this repo.

We have created progress branches - one branch per section - which include the completed content for that section. 

At any time if you get stuck and need to jump ahead to catch up, you can check out one of those branches. Branches are named `section-1` through `section-6`.

You will need to either commit or discard your changes when switching branches, and then can switch like so:

```
git checkout section-1
```

## Resources

- Docs: https://docs.evidence.dev
- Finished Project Repo: https://github.com/hughess/sf311
- Deployed Finished Project: https://sf311.evidence.app/

## Using Codespaces

If you are using this template in Codespaces, click the `Start Evidence` button in the bottom status bar. This will install dependencies and open a preview of your project in your browser - you should get a popup prompting you to open in browser.

Or you can use the following commands to get started:

```bash
npm install
npm run sources
npm run dev -- --host 0.0.0.0
```

See [the CLI docs](https://docs.evidence.dev/cli/) for more command information.

**Note:** Codespaces is much faster on the Desktop app. After the Codespace has booted, select the hamburger menu → Open in VS Code Desktop.


## Learning More

- [Docs](https://docs.evidence.dev/)
- [Github](https://github.com/evidence-dev/evidence)
- [Slack Community](https://slack.evidence.dev/)
- [Evidence Home Page](https://www.evidence.dev)
