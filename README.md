# checkout@v2
Fast and simple GitHub action to checkout large Git repos using --reference

It also saves space significantly.

Note: requires git version >= 2.35

# Usage
```yaml
- uses: tempest-tech-ltd/checkout@v2
  with:
    # GitHub repository name (with owner) or direct Git repository URL
    # Examples: tempest-tech-ltd/checkout, https://git.example.com/repo.git
    # Default:
    repository: ${{ github.repository }}

    # A token to fetch the repository. Typically, you would use GITHUB_TOKEN explicitly
    # Default:
    token: null

    # Common (reference) git repository path under GITHUB_WORKSPACE
    # Default:
    common-path: ${repository}.git

    # Relative path under GITHUB_WORKSPACE to place the repository
    # Default:
    path: null

    # A branch, tag or SHA to checkout
    # Default (if path is not null):
    ref: ${{ github.ref_name }}

    # Whether to clean working directory or not
    # Default:
    clean: true

    # Space-separated paths to restrict the working tree to. In cone mode these
    # are directories (top-level files are always included). In non-cone mode
    # these are gitignore-style patterns.
    # Default:
    sparse-checkout: null

    # Cone-mode sparse-checkout (faster on large repos, directories only).
    # 'true' or 'false'. Setting this or sparse-checkout enables sparse-checkout;
    # leave both empty for a full checkout.
    # Default:
    sparse-checkout-cone-mode: true
```

# Scenarios

## Typical checkout
```yaml
- uses: tempest-tech-ltd/checkout@v2
  with:
    token: ${{ secrets.GITHUB_TOKEN }}
    path: ${{ github.ref_name }}/src
```

## Checkout another branch keeping changes
```yaml
- uses: tempest-tech-ltd/checkout@v2
  with:
    token: ${{ secrets.GITHUB_TOKEN }}
    path: abranch-src
    ref: abranch
    clean: false
```

## Fetch or update reference (common) git directory only
```yaml
- uses: tempest-tech-ltd/checkout@v2
  with:
    token: ${{ secrets.GITHUB_TOKEN }}
```

## Fetch or update reference (common) git directory only of a public project
```yaml
- uses: tempest-tech-ltd/checkout@v2
  with:
    repository: chromium/chromium
```

## Checkout from a direct Git URL
```yaml
- uses: tempest-tech-ltd/checkout@v2
  with:
    repository: https://git.example.com/my-repo.git
    path: my-repo-src
```

## Sparse checkout (only some files in the working tree)
Useful when you only need to read/edit a few files (full history is still
available for committing/pushing).

Cone mode (default) — only top-level files plus the listed directories:
```yaml
- uses: tempest-tech-ltd/checkout@v2
  with:
    token: ${{ secrets.GITHUB_TOKEN }}
    path: my-repo-src
    sparse-checkout: src/app docs   # omit for top-level files only
```

Non-cone mode — gitignore-style patterns:
```yaml
- uses: tempest-tech-ltd/checkout@v2
  with:
    token: ${{ secrets.GITHUB_TOKEN }}
    path: my-repo-src
    sparse-checkout: Directory.Build.props
    sparse-checkout-cone-mode: false
```
Note: prefer cone mode. Non-cone patterns are gitignore-style, so a bare name
matches at any depth, and a leading slash (to anchor at the root) is mangled by
Git Bash/MSYS on Windows runners. If you only need top-level files, use cone
mode with an empty `sparse-checkout`.

## Checkout multiple repos and Push commits
Should just work as expected.
