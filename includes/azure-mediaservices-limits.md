>[!NOTE]
>För resurser som inte är fasta kan du be om att öka kvoterna genom att skapa ett supportärende. Försök **inte** få högre gränser genom att skapa ytterligare Azure Media Services-konton.

| Resurs | Standardgräns | 
| --- | --- | 
| Azure Media Services-konton (AMS) i en enskild prenumeration | 25 (fast) |
| Mediereserverade enheter per AMS-konto |25 (S1, S2)<br/>10 (S3) <sup>(1)</sup> | 
| Jobb per AMS-konto | 50,000<sup>(2)</sup> |
| Länkade uppgifter per jobb | 30 (fast) |
| Tillgångar per AMS-konto | 1,000,000|
| Tillgångar per uppgift | 50 |
| Tillgångar per jobb | 100 |
| Unik positionerare som är associerad med en tillgång vid ett tillfälle | 5<sup>(4)</sup> |
| Livekanaler per AMS-konto |5|
| Program i stoppat tillstånd per kanal |50|
| Program i körningstillstånd per kanal |3|
| Strömmande slutpunkter i körningstillstånd per AMS-konto|2|
| Strömningsenheter per slutpunkt för direktuppspelning |10 |
| Lagringskonton | 1&000;<sup>(5)</sup> (fast) |
| Principer | 1&000;&000;<sup>(6)</sup> |
| Filstorlek| I vissa fall finns det en gräns för maximal filstorlek för bearbetning i Media Services. <sup>7</sup> |
  
<sup>1</sup> S3 RU:er är inte tillgängliga i västra Indien.

<sup>2</sup> Det här värdet innefattar jobb i kö och avslutade, aktiva och avbrutna jobb. Det innefattar inte borttagna jobb. Du kan ta bort gamla jobb med **IJob.Delete** eller HTTP-begäran **DELETE**.

Från och med 1 april 2017 raderas alla jobbposter i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med deras associerade uppgiftsposter, även om det totala antalet poster är lägre än den maximala kvoten. Om du behöver arkivera jobb/uppgiftsinformationen kan du använda koden som beskrivs [här](../articles/media-services/media-services-dotnet-manage-entities.md).

<sup>3</sup> När du gör en begäran till jobblistan kan högst 1 000 entiteter returneras per begäran. Om du behöver följa upp alla skickade jobb kan du använda top/skip (maximalt antal poster som ska returneras/hoppa över) på det sätt som beskrivs i [OData system query options](http://msdn.microsoft.com/library/gg309461.aspx) (OData-systemfrågealternativ).

<sup>4</sup> Positionerare är inte utformade för att hantera åtkomstkontroll per användare. Om du vill ge olika åtkomsträttigheter till enskilda användare kan du använda DRM-lösningar (Digital Rights Management). Mer information finns i [det här](../articles/media-services/media-services-content-protection-overview.md) avsnittet.

<sup>5</sup> Lagringskontona måste tillhöra samma Azure-prenumeration.

<sup>6</sup> Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Du bör använda samma princip-ID om du alltid använder samma dagar, åtkomstbehörigheter etc. Mer information och ett exempel finns i [det här](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) avsnittet.

<sup>7</sup>Om du ska överföra innehåll till en tillgång i Azure Media Services med avsikt att bearbeta den med en av medieprocessorerna i vår tjänst (d.v.s. kodare som Media Encoder Standard och Media Encoder Premium Workflow eller analysmotorer som Face Detector) måste du känna till följande begränsningar. 

| Mediereserverad enhet | Maximal filstorlek (GB)| 
| --- | --- | 
|S1    | 325|
|S2    | 640|
|S3    | 260|
