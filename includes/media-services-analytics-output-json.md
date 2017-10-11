Jobbet producerar en JSON-utdata-fil som innehåller metadata om identifierade och spårade ytor. Metadata innehåller koordinater som anger platsen för ytor, samt ett ansikte ID-nummer som indikerar spårning av att enskilda. Ansikts-ID-nummer är lätt att återställa i fall när de främre står inför tappas bort eller överlappas i RAM, vilket resulterar i några personer komma tilldelade flera ID: N.

Utdata JSON innehåller följande attribut:

| Element | Beskrivning |
| --- | --- |
| Version |Detta refererar till versionen av Video-API. |
| Index | (Gäller bara för Azure Media Redactor) definierar ram index för den aktuella händelsen. |
| tidsrymd |”Tick” per sekund i videon. |
| förskjutning |Detta är tidsförskjutningen för tidsstämplar. I version 1.0 av API: er för Video att detta alltid vara 0. I framtiden scenarier som vi stöder det här värdet kan ändras. |
| ramhastighet |Bildrutor per sekund av videon. |
| fragment |Metadata är chunked upp till olika segment som kallas fragment. Varje avsnitt innehåller en start, varaktighet, antalet och händelse(r). |
| start |Starttiden för den första händelsen i 'tick'. |
| Varaktighet |Längden på ett fragment, i ”tick”. |
| intervall |Tidsintervall inom varje händelsepost i avsnittet i ”tick”. |
| evenemang |Varje händelse innehåller ytor identifieras och spåras inom den varaktigheten. Det är en matris med matris av händelser. Yttre matrisen representerar ett tidsintervall. Den interna matrisen består av 0 eller fler händelser som skedde vid den punkten i tid. En tom hakparentes [] innebär att inga ytor upptäcktes. |
| id |ID för de står inför som spåras. Det här antalet kan oavsiktligt ändra om ett ansikte blir oupptäckt. En viss person ska ha samma ID i hela övergripande video, men det går inte att garantera på grund av begränsningar i identifieringsalgoritm (är spärrat, etc.) |
| x, y |Det övre vänstra hörnet X och Y-koordinaterna för de står inför angränsande ruta i en normaliserade skala av 0,0 till 1,0. <br/>-X och Y koordinater är relativa till liggande alltid, så om du har en stående video (eller upp och ned, när det gäller iOS), måste du därför transponera koordinaterna. |
| bredd, höjd |Bredden och höjden på de står inför angränsande ruta i en normaliserade skala av 0,0 till 1,0. |
| facesDetected |Detta har hittats i slutet av JSON-resultaten och sammanfattar antal ytor algoritmen upptäcktes när videon. Eftersom dessa ID: N kan du återställa oavsiktligt om ett ansikte blir oupptäckt (t.ex. yta går utanför skärmen ser ut direkt) kan det här antalet kan inte alltid lika med true antal ytor i videon. |

