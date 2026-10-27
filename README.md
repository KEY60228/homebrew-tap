# KEY60228/homebrew-tap

個人用の Homebrew tap です。

## 使い方

```sh
brew tap KEY60228/tap
brew trust --tap KEY60228/tap
brew install --cask KEY60228/tap/<token>
```

Homebrew 7 以降では、第三者の tap は `brew trust` で信頼しないと読み込まれません。新しいマシンでは tap した後に 1 回実行します。

Brewfile:

```ruby
tap "KEY60228/tap"
cask "KEY60228/tap/<token>"
```

## Casks

| token | 説明 |
|---|---|
| [`alacritty`](#alacritty) | GPU-accelerated terminal emulator |

## 追加・更新の共通手順

cask は `Casks/<token>.rb` に置きます（formula を置く場合は `Formula/<name>.rb`）。

追加・更新したら、コミットの前に検証します。

```sh
brew style KEY60228/tap
brew audit --cask --strict --online KEY60228/tap/<token>
brew install --cask KEY60228/tap/<token>   # 更新時は brew upgrade --cask
```

push した内容は、各マシンで `brew update` を実行すると取り込まれます。

## alacritty

Homebrew 公式の `alacritty` cask は、Apple の公証を受けていないため 2026-09-01 に無効化されました（[alacritty/alacritty#8749](https://github.com/alacritty/alacritty/issues/8749)）。
この cask は、本家の GitHub Releases にある公式 DMG をインストールし、`postflight_steps` で `com.apple.quarantine` 属性を外します。

### 更新手順

1. 最新バージョンを確認する。

   ```sh
   gh release view -R alacritty/alacritty --json tagName --jq .tagName
   ```

2. DMG をダウンロードして sha256 を計算し、GitHub API の asset digest と一致することを確認する。

   ```sh
   V=0.17.0  # 新しいバージョン
   curl -fsSLO "https://github.com/alacritty/alacritty/releases/download/v$V/Alacritty-v$V.dmg"
   shasum -a 256 "Alacritty-v$V.dmg"
   gh api "repos/alacritty/alacritty/releases/tags/v$V" --jq '.assets[]|select(.name|endswith(".dmg")).digest'
   ```

3. `Casks/alacritty.rb` の `version` と `sha256` を書き換え、[共通手順](#追加更新の共通手順)で検証する。
