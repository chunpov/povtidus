# povtidus
good
name: Build Status

# Step 1: Build on pull-requests or pushes to master
on:
  push:
    branches:
    - master
  pull_request:
    branches:
    - master

jobs:
  # Step 2: Build the samples
  build-java:
    name: Build
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        regex:
        - "java/advanced"
        - "kotlin/advanced"
        - "java/admob"
        - "kotlin/admob"
        - "java/admanager"
        - "kotlin/admanager"
    steps:
      - name: Set up JDK 1.8
        uses: actions/setup-java@v1
        with:
          java-version: 1.8

      - name: Clone Repo
        uses: actions/checkout@v1

      - name: Set commit range (push to the master branch, e.g. merge)
        if: github.ref == 'refs/heads/master' && github.event_name == 'push'
        run: echo "COMMIT_RANGE="${{ github.event.before }}.."" >> $GITHUB_ENV

      - name: Set commit range (pull request)
        if: github.event_name == 'pull_request'
        run: echo "COMMIT_RANGE="HEAD~.."" >> $GITHUB_ENV

      - name: Build
        run: .github/workflows/build/build.sh
        shell: bash
        env:
          COMMIT_RANGE: ${{ env.COMMIT_RANGE }}
          REGEX: ${{ matrix.regex }}
