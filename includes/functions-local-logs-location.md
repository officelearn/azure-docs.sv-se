När funktioner värden körs lokalt, skriver loggar till följande sökväg:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

I Windows, `<DefaultTempDirectory>` är det första värdet som hittades av TMP TEMP USERPROFILE miljövariabler eller Windows-katalogen.
I MacOS eller Linux `<DefaultTempDirectory>` TMPDIR miljövariabel.

[!Note]
När värden funktioner startar skrivs den befintliga filstrukturen i katalogen.