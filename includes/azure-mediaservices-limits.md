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
| Lagringskonton | 1 000<sup>(5)</sup> (fast) |
| Principer | 1 000 000<sup>(6)</sup> |
| Filstorlek| I vissa situationer kan finns det en gräns på den största filstorleken som stöds för bearbetning i Media Services. <sup>7</sup> |
  
<sup>1</sup> S3 RU:er är inte tillgängliga i västra Indien. Om du ändrar typ (till exempel från S2 till S1) återställs max RU-gränser.

<sup>2</sup> Det här värdet innefattar jobb i kö och avslutade, aktiva och avbrutna jobb. Det innefattar inte borttagna jobb. Du kan ta bort gamla jobb med **IJob.Delete** eller HTTP-begäran **DELETE**.

Från och med 1 April 2017 raderas alla jobb poster i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med dess associerade aktiviteten poster, även om det totala antalet poster som är lägre än den maximala kvoten. Om du behöver arkivera jobb/uppgiftsinformationen kan du använda koden som beskrivs [här](../articles/media-services/media-services-dotnet-manage-entities.md).

<sup>3</sup> när du gör en begäran till listan jobbet entiteter kan högst 1 000 jobben returneras per begäran. Om du behöver följa upp alla skickade jobb kan du använda top/skip (maximalt antal poster som ska returneras/hoppa över) på det sätt som beskrivs i [OData system query options](http://msdn.microsoft.com/library/gg309461.aspx) (OData-systemfrågealternativ).

<sup>4</sup> Positionerare är inte utformade för att hantera åtkomstkontroll per användare. Om du vill ge olika åtkomsträttigheter till enskilda användare kan du använda DRM-lösningar (Digital Rights Management). Mer information finns i [det här](../articles/media-services/media-services-content-protection-overview.md) avsnittet.

<sup>5</sup> Lagringskontona måste tillhöra samma Azure-prenumeration.

<sup>6</sup> Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Du bör använda samma princip-ID om du alltid använder samma dagar, åtkomstbehörigheter etc. Mer information och ett exempel finns i [det här](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) avsnittet.

<sup>7</sup>om du överför innehåll till en tillgång i Azure Media Services för att bearbeta den med något av de media processorerna i tjänsten (kodare som Media Encoder Standard och Media Encoder Premium arbetsflöde eller analys motorer som som är ansikte detektor) sedan måste du vara medveten om begränsningarna i de maximala storlekar som stöds. 

Den maximala storleken som stöds för en enda blob är för närvarande upp till 5 TB i Azure Blob Storage. Dock gäller ytterligare begränsningar i Azure Media Services baserat på storlek på Virtuella datorer som används av tjänsten. I följande tabell visas begränsningarna på varje Media reserverade enheter (S1 S2, S3.) Om din källfil är större än de gränser som definierats i tabellen, misslyckas kodning jobbet. Om du kodar 4K upplösning källor långvarig behöver du använda S3 Media reserverade enheter för att uppnå de prestanda som behövs. Om du har 4K-innehåll som är större än gränsen på 260 GB på S3 Media reserverade enheter kontaktar du oss på amshelp@microsoft.com för eventuella åtgärder som stöd för ditt scenario.

| Mediereserverad enhet | Maximal inkommande storlek (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
