1) Workflows - attached to repo; contains one or more jobs; triggered by events
2) Jobs - define a runner (execution env); contain one or more steps; can be conditional; executed in parallel by default (can be updated to series)
3) Steps - belong to jobs; either a shell script or an Action (predefined tasks); executed in series; can be conditional
### Example Workflow
```yaml
name: First Workflow
on: workflow_dispatch
jobs:
  first-job:
    runs-on: ubuntu-latest
    steps:
      - name: Print greeting
        run: echo "Hello World!"
      - name: Print goodbye
        run: echo "Done - bye!"
```

> multiline commands
```yaml
run: |
	echo "First output"
	echo "Second output"
```

> example triggers - search github actions events
- eg pull request opened

#### Example to 
```yaml
name: Test workflow
on: push
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install nodejs
        uses: actions/setup-node@v3
        with:
          node-version: 18
      - name: Install deps
        run: npm ci
      - name: Run tests
        run: npm run test
```

> every job has its own runner
> we can make jobs go in serial with a 'needs' key (like docker compose dependency)
```yaml
name: Test workflow
on: push
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install nodejs
        uses: actions/setup-node@v3
        with:
          node-version: 18
      - name: Install deps
        run: npm ci
      - name: Run tests
        run: npm run test
  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install nodejs
        uses: actions/setup-node@v3
        with:
          node-version: 18
      - name: Install deps
        run: npm ci
      - name: Build project
        run: npm run build
      - name: Deploy
        run: echo "deploying"
```

> github context
```yaml
name: Output Information
on: workflow_dispatch
jobs:
  info:
    runs-on: ubuntu-latest
    steps:
      - name: print
        run: echo "${{toJson(github)}}"
```

```json
{
  token: ***,
  job: info,
  ref: refs/heads/main,
  sha: abc,
  repository: bronifty/delete-me-1,
  repository_owner: bronifty,
  repository_owner_id: 86934366,
  repositoryUrl: git://github.com/bronifty/delete-me-1.git,
  ...
}```

