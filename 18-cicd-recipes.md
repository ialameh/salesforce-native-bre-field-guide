# 18. CI/CD Recipes

This chapter provides concrete pipeline configurations for deploying native BRE rules with GitHub Actions.

## General considerations

Native BRE metadata deploys as standard Salesforce metadata. Activation is always manual. Plan for this in your deployment runbook.

## GitHub Actions recipe

```yaml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install Salesforce CLI
        run: |
          npm install -g sf-cli
          sf --version

      - name: Authenticate to org
        run: |
          sf org login jwt \
            --username ${{ vars.PROD_USERNAME }} \
            --jwt-key-file secrets/jwt.key \
            --client-id ${{ secrets.CONNECTED_APP_CLIENT_ID }} \
            --set-default

      - name: Deploy BRE metadata
        run: |
          sf project deploy start \
            --target-org production \
            --sourcepath force-app/main/default/expressionSets \
            --sourcepath force-app/main/default/decisionMatrix \
            --wait 10

      - name: Notify activation needed
        run: |
          echo "BRE rules deployed as drafts. Navigate to Setup > Business Rules Engine to activate each one."
```

## Post-deployment activation

Since activation is manual, add a notification step to your pipeline to alert the right people:

```yaml
- name: Notify BRE activation needed
  run: |
    echo "Expression Sets deployed. Activate at: https://<org>/lightning/setup/ExpressionSets/home"
```

## Selective deploy with forceignore

To manage only a subset of BRE rules in source control:

```
**/expressionSets/**
**/decisionMatrix/**
```

Add BRE paths to `forceignore` for managed-only rules.

## Handling large matrices in CI

If a Decision Matrix has 50,000+ rows, the row files can bloat your repository. Use a shallow clone:

```yaml
- uses: actions/checkout@v4
  with:
    fetch-depth: 1
```

## Running tests in CI

```bash
sf apex test run --test-level RunLocalTests --result-format human
```

## References

- [Salesforce CLI Reference](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_plugins.meta/sfdx_cli_plugins/cli_project_deploy.htm)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
