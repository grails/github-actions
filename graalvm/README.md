# Github composite actions for graalvm workflow

Usage example:
```yaml
name: GraalVM Latest CI
on:
  push:
    branches:
      - master
      - '[1-9]+.[0-9]+.x'
  pull_request:
    branches:
      - master
      - '[1-9]+.[0-9]+.x'
jobs:
  build:
    if: github.repository != 'micronaut-projects/micronaut-project-template'
    runs-on: ubuntu-latest
    strategy:
      matrix:
        graalvm: [ 'latest' ]
        java: [ '17' ]
    env:
      GRADLE_ENTERPRISE_ACCESS_KEY: ${{ secrets.GRADLE_ENTERPRISE_ACCESS_KEY }}
      GRADLE_ENTERPRISE_CACHE_USERNAME: ${{ secrets.GRADLE_ENTERPRISE_CACHE_USERNAME }}
      GRADLE_ENTERPRISE_CACHE_PASSWORD: ${{ secrets.GRADLE_ENTERPRISE_CACHE_PASSWORD }}
    steps:
      - uses: actions/checkout@v3
      - name: Pre-Build Steps
        uses: micronaut-projects/github-actions/graalvm/pre-build@master
        id: pre-build
        with:
          graalvm: ${{ matrix.graalvm }}
          java: ${{ matrix.java }}
      - name: Build Steps
        uses: micronaut-projects/github-actions/graalvm/build@master
        id: build
        env:
          GH_TOKEN_PUBLIC_REPOS_READONLY: ${{ secrets.GH_TOKEN_PUBLIC_REPOS_READONLY }}
          GH_USERNAME: ${{ secrets.GH_USERNAME }}
      - name: Post-Build Steps
        uses: micronaut-projects/github-actions/graalvm/post-build@master
        id: post-build
        with:
          java: ${{ matrix.java }}
```