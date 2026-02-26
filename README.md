# Obfuscura GitHub Action

[![GitHub Marketplace](https://img.shields.io/badge/marketplace-obfuscura--encode-green?logo=github)](https://github.com/marketplace/actions/obfuscura-encode)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

Encode PHP files with [Obfuscura](https://obfuscura.com) directly in your GitHub Actions workflow. Ship protected builds automatically on every release.

## Usage

```yaml
# .github/workflows/release.yml
name: Build & Encode

on:
  push:
    tags:
      - 'v*'

jobs:
  encode:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build your project
        run: |
          composer install --no-dev
          zip -r dist/my-plugin.zip src/ vendor/ composer.json

      - name: Encode PHP files
        uses: obfuscura/github-action@v1
        with:
          api-key: ${{ secrets.OBFUSCURA_API_KEY }}
          source: dist/my-plugin.zip

      - name: Upload encoded artifact
        uses: actions/upload-artifact@v4
        with:
          name: my-plugin-encoded
          path: dist/my-plugin-encoded.zip
```

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `api-key` | **Yes** | — | Your Obfuscura project API key. Store as a [GitHub secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets). |
| `source` | **Yes** | — | Path to the `.php` file or `.zip` archive to encode. |
| `output` | No | Auto | Output path. Defaults to `{source}-encoded.{ext}`. |
| `string-encryption` | No | `true` | Encrypt all string literals. |
| `control-flow` | No | `true` | Restructure logic flow to resist decompilation. |
| `dead-code` | No | `false` | Inject realistic non-functional code paths. |
| `license-binding` | No | `true` | Require valid license at runtime. |
| `include-loader` | No | `true` | Download and include the runtime loader alongside output. |
| `api-url` | No | `https://obfuscura.com/api/v1` | Override for enterprise/self-hosted instances. |

## Outputs

| Output | Description |
|---|---|
| `encoded-file` | Path to the encoded output file. |
| `loader-file` | Path to the downloaded loader file (if `include-loader` is `true`). |

## Examples

### Encode a single file

```yaml
- uses: obfuscura/github-action@v1
  with:
    api-key: ${{ secrets.OBFUSCURA_API_KEY }}
    source: src/LicenseCheck.php
    output: dist/LicenseCheck.php
```

### Encode with all protections

```yaml
- uses: obfuscura/github-action@v1
  with:
    api-key: ${{ secrets.OBFUSCURA_API_KEY }}
    source: dist/my-plugin.zip
    string-encryption: 'true'
    control-flow: 'true'
    dead-code: 'true'
    license-binding: 'true'
```

### Encode and create a GitHub Release

```yaml
- name: Encode
  id: encode
  uses: obfuscura/github-action@v1
  with:
    api-key: ${{ secrets.OBFUSCURA_API_KEY }}
    source: dist/my-plugin.zip

- name: Create Release
  uses: softprops/action-gh-release@v2
  with:
    files: ${{ steps.encode.outputs.encoded-file }}
```

## Getting Your API Key

1. Sign in at [obfuscura.com](https://obfuscura.com)
2. Go to your project settings
3. Copy the **Project API Key**
4. Add it as a repository secret named `OBFUSCURA_API_KEY`

## Security

Your API key is never logged or exposed. The action uses HTTPS for all API communication. See our [security practices](https://obfuscura.com/security).

## License

MIT — see [LICENSE](LICENSE) for details.
