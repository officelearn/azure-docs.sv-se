Jobbet producerar en JSON-utdata-fil som innehåller metadata om identifierade och spårade ansikten. Metadata innehåller koordinater som anger platsen för ansikten, samt ett face ID-nummer som indikerar spårningen av den individen. Face ID-nummer är lätt att återställa under omständigheter när främre ansiktet förloras eller överlappas i RAM, vilket resulterar i vissa personer komma tilldelats flera ID: N.

Utdata JSON innehåller följande element:

### <a name="root-json-elements"></a>Rot JSON-element

| Element | Beskrivning |
| --- | --- |
| version |Detta refererar till versionen av Video-API. |
| tidsskalan |”Ticken” per sekund av videon. |
| förskjutning |Det här är tidsförskjutningen för tidsstämplar. Det kommer alltid ske 0 i version 1.0 av Video-API: er. I framtiden scenarier som vi har stöd för det här värdet kan ändras. |
| bredd, High |Bredden och High för utdata video ramen, i bildpunkter.|
| ramhastighet |Bildrutor per sekund av videon. |
| [fragment](#fragments-json-elements) |Metadata är segmentvis upp i olika segment som kallas fragment. Varje fragment innehåller en start, varaktighet, antalet och händelse(r). |

### <a name="fragments-json-elements"></a>Fragment JSON-element

|Element|Beskrivning|
|---|---|
| start |Starttiden för den första händelsen i ”ticken”. |
| varaktighet |Längden på ett fragment, i ”ticken”. |
| index | (Gäller bara för Azure Media Redactor) definierar ramens index för den aktuella händelsen. |
| interval |Intervallet för varje händelsepost i avsnittet i ”ticken”. |
| evenemang |Varje händelse innehåller ansikten har identifierats och spåras inom den varaktigheten. Det är en matris med händelser. Yttre matrisen representerar en viss tidsperiod. Den inre matrisen består av 0 eller fler händelser som inträffade vid den tidpunkten i tid. En tom hakparentes [] innebär inga ansikten har identifierats. |
| id |ID för ansiktet som spåras. Det här talet ändras oavsiktligt om ett ansikte blir oidentifierade. En viss person bör ha samma ID i hela den övergripande videon, men detta kan inte garanteras på grund av begränsningar i identifieringsalgoritm (ocklusion, osv.). |
| x, y |Övre vänstra hörnet X och Y-koordinaterna för de står inför angränsande ruta i en normaliserad skala av 0,0 till 1,0. <br/>-X- och Y koordinaterna är relativ till liggande alltid, så om du har en stående video (eller upp och ned, när det gäller iOS), måste du transponera koordinaterna i enlighet med detta. |
| bredd, höjd |Bredden och höjden på ansiktet angränsande ruta i en normaliserad skala av 0,0 till 1,0. |
| facesDetected |Detta finns i slutet av JSON-resultaten och sammanfattar hur många ansikten som algoritmen har identifierats under videon. Eftersom de ID: N kan återställas oavsiktligt om ett ansikte blir oidentifierade (t.ex. de står inför går utanför skärmen ser direkt), det här antalet kan inte alltid lika med SANT antalet ansikten i videon. |
