```
╔═║╔═║╔═ ╔═ ╔═║╔╔    ╔═╝╔═║╔═║╔╔ ╔═╝╔═╝
╔╔╝╔═║║ ║║ ║║ ║║║║   ╔═╝╔╔╝╔═║║║║╔═╝══║
╝ ╝╝ ╝╝ ╝══ ══╝╝╝╝══╝╝  ╝ ╝╝ ╝╝╝╝══╝══╝

```

## random_frames - Contract Testing for APIs

**verify api backwards compatibility before deploy**

### install

```bash
pip install random_frames
```

### record baseline

```bash
# capture current api behavior
random_frames record https://api.example.com \
  --output baseline.json \
  --endpoints /users,/posts,/comments
```

### verify changes

```bash
# test against baseline
random_frames verify https://staging.api.example.com \
  --baseline baseline.json
```

output:

```
✓ GET /users - compatible
✓ GET /posts - compatible
✗ GET /comments - BREAKING CHANGE
  - field 'author' removed
  - field 'likes' type changed: number → string
```

### features

- detects breaking changes
- schema drift analysis
- response time regression
- works with rest, graphql

powered by **schemadiff-engine** ([schemadiff-engine.io](https://schemadiff-engine.io))

### ci/cd

```yaml
# gitlab-ci.yml
test:
  script:
    - random_frames verify $STAGING_URL --baseline prod-baseline.json
    - if [ $? -ne 0 ]; then exit 1; fi
```

### ignore rules

```yaml
# .random_frames.yaml
ignore:
  - path: /internal/*
  - field: metadata.timestamp  # always changes
  - header: X-Request-ID
```

### modes

```bash
# strict mode (any change = fail)
random_frames verify --mode strict

# loose mode (only breaking changes fail)
random_frames verify --mode loose

# report mode (never fail, just report)
random_frames verify --mode report
```

### comparison

| tool | rest | graphql | price |
|------|------|---------|-------|
| random_frames | ✓ | ✓ | free |
| pact | ✓ | ✗ | free |
| postman | ✓ | ✓ | paid |

### limitations

- max 100 endpoints per baseline
- graphql support experimental
- no websocket support yet

Apache-2.0
