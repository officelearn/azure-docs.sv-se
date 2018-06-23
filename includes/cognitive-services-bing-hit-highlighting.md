Bing stöder träffar syntaxmarkering som markerar sökord (eller andra villkor som Bing hittar relevanta) i visningssträngar på några av svar. Till exempel en webbsida `name`, `displayUrl`, och `snippet` fält kan markera sökord.

Som standard innehåller Bing syntaxmarkering markörer i visningssträngar. Om du vill inkludera markörerna inkluderar den `textDecorations` Frågeparametern i din begäran och ange det till **SANT**. Bing markerar sökord som använder E000 och E001 Unicode-tecken för att markera början och har löpt ut. Till exempel om frågetermen är färdas tala och antingen termen finns i fältet omges termen av träffar färgmarkera tecken som visas i följande exempel:  
  
![Klicka på markering](./media/cognitive-services-bing-hit-highlighting/bing-hit-highlighting.PNG) 

Innan du kan visas strängen i användargränssnittet, ska du ersätta Unicode-tecken med tecken som är lämpliga för din visningsformatet. Till exempel om texten som visas i HTML-format, du kan markera frågetermen genom att ersätta E000 med < b\> och E001 med < /b\>. Om du inte vill använda formatering bort markörerna från strängen. 

Bing ger möjlighet att använda Unicode-tecken eller HTML-taggar som markörer. Om du vill ange vilka markörer att använda inkluderar den `textFormat` Frågeparametern. Om du vill markera innehåll med Unicode-tecken, ange `textFormat` Raw (standard) och markera innehåll med HTML-taggar måste ange `textFormat` till HTML. 
  
Om `textDecorations` är **SANT**, Bing kan även inkludera följande markörer i visningssträngar av svar. Om det finns ingen HTML-motsvarighet, är tabellcell HTML tom.

|Unicode|HTML|Beskrivning
|-|-|-
|U + E000|\<b >|Markerar starten av frågeterm (träffar markering)
|U + E001|\</b >|Markerar slutet av frågetermen
|U + E002|\<Jag >|Markerar starten av kursiv stil innehåll 
|U + E003|\</i >|Markerar slutet av kursiv stil innehåll
|U + E004|\<br / >|Markerar en radbrytning
|U + E005||Markerar starten av ett telefonnummer
|U + E006||Markerar slutet på ett telefonnummer
|U + E007||Markerar starten av en adress
|U + E008||Markerar slutet på en adress
|U + E009|\&nbsp;|Markerar ett hårt blanksteg
|U + E00C|\<strong >|Markerar starten av fetstil innehåll
|U + E00D|\</ strong >|Markerar slutet av fetstil innehåll
|U + E00E||Markerar starten av innehåll vars bakgrund ska vara lättare än omgivande bakgrunden
|U + E00F||Markerar slutet av innehåll vars bakgrund ska vara lättare än omgivande bakgrunden
|U + E010||Markerar starten av innehåll vars bakgrund ska vara mörkare omgivande bakgrunden
|U + E011||Markerar slutet av innehåll vars bakgrund ska vara mörkare omgivande bakgrunden
|U + E012|\<del >|Markerar starten av innehåll som ska vara träffas via
|U + E013|\<del >|Markerar slutet av innehåll som ska vara träffas via
|U + E016|\<Sub >|Markerar starten av nedsänkt innehåll
|U + E017|\</ sub >|Markerar slutet på nedsänkt innehåll
|U + E018|\<sup >|Markerar starten av upphöjd innehåll
|U + E019|\</ sup >|Markerar slutet av upphöjd innehåll

Följande exempel visar en `Computation` svar som innehåller nedsänkt markörer för en log(2) frågeterm. Den `expression` fältet innehåller markörer bara om `textDecoration` är **SANT**.

![beräkning markörer](./media/cognitive-services-bing-hit-highlighting/bing-markers-computation.PNG) 

Om begäran inte har begärt dekorationer, är uttrycket log10(2). 
  
