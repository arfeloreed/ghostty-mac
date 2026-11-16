# Gabay sa Aking Ghostty Setup

Huling inayos: 2026-11-16. Ghostty 1.4.0 (naka-install gamit ang Homebrew), sa MacBook.

Ito ang paliwanag kung ano ang laman ng Ghostty config ko, saan makikita ang bawat file, at paano ito baguhin nang ligtas.

---

## 1. Nasaan ang mga file

| Ano | Saan |
| --- | --- |
| Lahat ng setting ng Ghostty (ito mismong folder) | `~/.config/ghostty/` |
| Ang config na binabasa ng Ghostty | `~/.config/ghostty/config.ghostty` |
| Ang gabay na ito | `~/.config/ghostty/README.md` |
| Mga tagubilin para kay Claude (nasa git) | `~/.config/ghostty/CLAUDE.md` |
| Mga command na pwedeng patakbuhin ni Claude nang hindi nagtatanong (wala sa git) | `~/.config/ghostty/.claude/settings.json` |
| Listahan ng mga file na hindi isasama sa git | `~/.config/ghostty/.gitignore` |
| Personal kong notes (wala sa git) | `~/.config/ghostty/reed-docs/` |
| Kopya sa GitHub (public, kita ng lahat) | https://github.com/arfeloreed/ghostty-mac |

Isang file lang ang config: `config.ghostty`. Huwag gumawa ng file na `config` sa folder na ito, o ng kahit anong file sa `~/Library/Application Support/com.mitchellh.ghostty/`. Binabasa rin ng Ghostty ang mga iyon, at ang huling nabasa ang nasusunod. Kaya kung nasa dalawang file ang isang setting, mahirap malaman kung alin ang gumagana.

Para buksan ang config: pindutin ang `Command+,` sa loob ng Ghostty, o sa terminal:

```
nvim ~/.config/ghostty/config.ghostty
```

---

## 2. Ano ang laman ng config

| Setting | Value | Ibig sabihin |
| --- | --- | --- |
| Theme (`theme`) | `Nord` | Ang mga kulay ng terminal. |
| Font (`font-size`) | JetBrains Mono, `18` | Sariling font ng Ghostty. Ito ang ginagamit kasi walang `font-family` sa config. Galing din sa Ghostty ang mga icon (Symbols Nerd Font). Mas malaki ang `18` kaysa sa default na `13`. |
| Linis ng gilid ng text (`alpha-blending`) | `linear-corrected` | Inaalis ang maitim na anino sa gilid ng makukulay na letra (halimbawa pula sa berde). Halos pareho pa rin ang kapal ng text. Sa Mac, `native` ang default. |
| Pinakamababang contrast (`minimum-contrast`) | `1.1` | Kapag halos kapareho ng background ang kulay ng text, pinapaliwanag ito nang konti para hindi mawala. Mababa ang `1.1` kaya hindi nito ginagalaw ang karaniwang kulay ng theme. `1` (walang ginagawa) ang default. |
| Kulay ng background (`background`) | `#1c1f2a` | Mas madilim kaysa sa sariling kulay ng Nord (`#2e3440`), para mabasa pa rin ang text kahit maliwanag ang desktop sa likod. |
| Kapal ng background (`background-opacity`) | `0.80` | `1` ay buo at hindi tagos, `0` ay kita ang lahat sa likod. Ako mismo ang nag-adjust nito. |
| Labo ng nasa likod (`background-blur`) | `20` | Gaano kalabo ang desktop na nakikita sa likod. `20` ang sariling default ng Ghostty. |
| Puwang sa gilid (`window-padding-x`, `window-padding-y`) | `3` sa kaliwa at kanan, `0` sa taas at baba | Manipis na puwang sa kaliwa at kanan, wala sa taas at baba. `2` sa lahat ng gilid ang default ng Ghostty. |
| Pantay na puwang (`window-padding-balance`) | `true` | Bihirang sakto ang laki ng window sa bilang ng letra, kaya may natitirang konting puwang. Hinahati ito sa apat na gilid imbes na maipon sa kanan at sa ibaba. Hindi pinapalaki nang sobra ang puwang sa taas; ang sobra ay sa ibaba napupunta. |
| Pagsara ng window o tab (`confirm-close-surface`) | `always` | Laging nagtatanong bago magsara, kahit walang tumatakbong program. Sa default ng Ghostty, nagtatanong lang kapag may tumatakbo pa. |

---

## 3. Paano magbago ng setting

1. Buksan ang config (`Command+,`), baguhin, at i-save.
2. Suriin kung tama ang sinulat. Sa terminal, sa loob ng folder na ito (`cd ~/.config/ghostty`):

   ```
   ghostty +validate-config
   ```

   Kapag walang lumabas, ayos. Kapag may mali, sasabihin nito kung anong linya.
3. I-reload ang Ghostty: `Command+Shift+,`. Hindi ito kusang nagre-reload pagka-save.
4. Tingnan ang window kung tama ang itsura.
5. Kapag maayos, i-commit sa git, tapos i-push sa GitHub.

Mga paalala:

- **Kapal ng background:** ang pagbago ng `background-opacity` ay hindi gumagana sa reload. Isara ang buong Ghostty (`Command+Q`) at buksan ulit.
- **Puwang sa gilid:** ang pagbago ng `window-padding-x`, `window-padding-y` at `window-padding-balance` ay sa bagong window o tab lang lalabas.
- **Comment:** ang `#` ay sa simula lang ng linya gumagana. Kapag nilagay sa dulo ng isang setting, magiging bahagi ito ng value at masisira ang setting.
- **Git branch:** kung anong branch ang nakabukas, iyon ang config na babasahin ng Ghostty sa susunod na reload.

---

## 4. Kapag may nasira: ibalik ang dati

Naka-git ang folder na ito, kaya nandoon pa ang huling na-commit na config. Sa terminal:

```
cd ~/.config/ghostty
git diff config.ghostty
```

Ipinapakita nito kung ano ang nagbago mula sa huling commit. Para ibalik sa huling commit:

```
git restore config.ghostty
```

Tapos i-reload (`Command+Shift+,`). Para makita ang lahat ng dating commit: `git log --oneline`.

---

## 5. Mga kapaki-pakinabang na command

Patakbuhin sa terminal. Kung ang terminal ay hindi Ghostty, gamitin ang buong path: `/Applications/Ghostty.app/Contents/MacOS/ghostty` sa halip na `ghostty`.

| Command | Gagawin |
| --- | --- |
| `ghostty +validate-config` | Suriin kung may mali sa config |
| `ghostty +explain-config <setting>` | Ipaliwanag ang isang setting, halimbawa `ghostty +explain-config font-size` |
| `ghostty +list-themes` | Silipin ang lahat ng theme na pwedeng gamitin (`Esc` para lumabas) |
| `ghostty +show-face --string=a` | Alamin kung anong font ang talagang ginagamit |
| `ghostty +list-keybinds --default` | Ipakita ang lahat ng default na shortcut |
| `ghostty +version` | Alamin ang bersyon ng Ghostty |

---

## 6. Paano mag-update ng Ghostty

```
brew upgrade --cask ghostty
```

Pagkatapos, patakbuhin ang `ghostty +validate-config`. Kapag may setting na hindi na tinatanggap ng bagong bersyon, doon ito lalabas.
