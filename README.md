# LT Slides
LTで使用したSlideとTalkScriptをまとめる

## Prerequisites
### Deck
- **deck**: Install via Homebrew
  ```bash
  arch -arm64 brew install deck
  ```

- **Google API Credentials**: 
  - Enable Google Slides API and Google Drive API
  - Download credentials to `~/.local/share/deck/credentials.json`

### marp
- *marp: Install marp-cli via Homebrew


### slide generation
- deck


- marp

```
marp --pptx slide-deck.md
marp slide-deck.md -o converted.pptx
```

## Reference 

For deck documentation, see: [k1LoW/deck](https://github.com/k1LoW/deck)
For marp documentation, see: [marp-team/marp-cli](https://github.com/marp-team/marp-cli)
