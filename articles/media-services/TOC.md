# [Översikt](media-services-overview.md)
## [Koncept](media-services-concepts.md)

# Kom igång
## [Skapa och hantera konto](media-services-portal-create-account.md)
## [Konfigurera din utvecklingsmiljö](media-services-set-up-computer.md)
###[NET](media-services-dotnet-how-to-use.md)
###[REST](media-services-rest-how-to-use.md)  
## Ansluta genom programmering
### [.NET](media-services-dotnet-connect-programmatically.md)
### [REST](media-services-rest-connect-programmatically.md)

## Leverera video på begäran
### [Portal](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Utföra liveuppspelning
### [Portal](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Gör så här för att
## Hantera
### Konton
#### [PowerShell](media-services-manage-with-powershell.md)
#### [REST](/rest/api/media/mediaservice)
### Entiteter
#### [NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Slutpunkter för direktuppspelning](media-services-streaming-endpoints-overview.md)
#### [Portalen](media-services-portal-manage-streaming-endpoints.md)
#### [NET](media-services-dotnet-manage-streaming-endpoints.md)
### Lagring
#### [Uppdatera Media Services efter återställning av lagringsåtkomstnycklar](media-services-roll-storage-access-keys.md)
#### [Hantera tillgångar över flera lagringskonton](meda-services-managing-multiple-storage-accounts.md)
### [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

## Överföra innehåll
### Överföra filer till ett konto
#### [Portal](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Överföra stora filer med Aspera](media-services-upload-files-with-aspera.md)
### [Ladda upp filer med StorSimple](media-services-upload-files-from-storsimple.md)
### [Kopiera befintliga blobar](media-services-copying-existing-blob.md)

## [Koda innehåll](media-services-encode-asset.md)
### [Jämför kodare](media-services-compare-encoders.md)
### [Hantera hastighet och samtidighet för kodningen](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Format och codecs för Media Encoder Standard](media-services-media-encoder-standard-formats.md)
#### [Använda MES för att generera en bithastighetsstege automatiskt](media-services-autogen-bitrate-ladder-with-mes.md)
#### Koda med Media Encoder Standard
##### [Portal](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [Avancerad kodning med MES](media-services-advanced-encoding-with-mes.md)
##### [Anpassa förval för Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
##### [Generera miniatyrer med Media Encoder Standard med .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Beskära videoklipp med Media Encoder Standard](media-services-crop-video.md)
#### MES-scheman
##### [Media Encoder Standard-schema](media-services-mes-schema.md)
##### [Inkommande metadata](media-services-input-metadata-schema.md)
##### [Utgående metadata](media-services-output-metadata-schema.md)
#### [MES-förinställningar](media-services-mes-presets-overview.md) 
##### [H264, flera bithastigheter, 1080p, Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264, flera bithastigheter, 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264, flera bithastigheter, 16.9, SD, Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264, flera bithastigheter, 16x9, SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [H264, flera bithastigheter, 16x9 för iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264, flera bithastigheter, 4K, Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264, flera bithastigheter, 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264, flera bithastigheter, 4x3, SD, Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264, flera bithastigheter, 4x3, SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [H264, flera bithastigheter, 4x3 för iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264, flera bithastigheter, 720p, Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264, flera bithastigheter, 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264, enkel bithastighet, 1080p, Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264, enkel bithastighet, 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264, enkel bithastighet, 16x9, SD, Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264, enkel bithastighet, 16x9, SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264, enkel bithastighet, 4K, Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264, enkel bithastighet, 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264, enkel bithastighet, 4x3, SD, Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264, enkel bithastighet, 4x3, SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264, enkel bithastighet, 720p, Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264, enkel bithastighet, 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [H264, enkel bithastighet, 720p för Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [H264, enkel bithastighet, hög kvalitet, SD för Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [H264, enkel bithastighet, låg kvalitet, SD för Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### Arbetsflöde för Media Encoder Premium
#### [Arbetsflödesformat och codecs för Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)
#### Koda med Media Encoder Premium-arbetsflöde
##### [Arbetsflöde för Media Encoder Premium](media-services-encode-with-premium-workflow.md)
##### [Självstudier för arbetsflödet för Media Encoder Premium](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Skapa avancerade arbetsflöden för kodning med Workflow Designer](media-services-workflow-designer.md)
##### [Premium-arbetsflöde med flera indatafiler](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [Skapa en uppgift som genererar fMP4-segment](media-services-generate-fmp4-chunks.md)
### Mediebearbetare
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [Felkoder](media-services-encoding-error-codes.md)
### Inaktuell
#### [Statisk paketering och kryptering](media-services-static-packaging.md)

## [Strömma live](media-services-manage-channels-overview.md)
### [Lokala kodare](media-services-live-streaming-with-onprem-encoders.md)
#### [Portal](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
#### [REST](https://docs.microsoft.com/rest/api/media/operations/channel)
### [Liveuppspelning med molnkodare](media-services-manage-live-encoder-enabled-channels.md)
#### [Portalen](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [Konfigurera lokala kodare för användning med molnkodare](media-services-live-encoders-overview.md)
#### [Elemental Live-kodare](media-services-configure-elemental-live-encoder.md)
#### [FMLE-kodare](media-services-configure-fmle-live-encoder.md)
#### [NewTek TriCaster-kodare](media-services-configure-tricaster-live-encoder.md)
#### [Wirecast-kodare](media-services-configure-wirecast-live-encoder.md)
### [Hantera långvariga åtgärder](media-services-dotnet-long-operations.md)
### [Specifikation för live-inmatning av fragmenterad MP4](media-services-fmp4-live-ingest-overview.md)

## [Skydda](media-services-content-protection-overview.md)
### [Konfigurera innehållsskydd i portalen](media-services-portal-protect-content.md)
### [Konfigurera AES-128-klartextnyckel för din dataström](media-services-protect-with-aes128.md)
### [Använd REST för att kryptera ditt innehåll med lagringskryptering](media-services-rest-storage-encryption.md)
### [Översikt för Media Services PlayReady-licensmall](media-services-playready-license-template-overview.md)
### [Översikt över Widevine-licensmallen](media-services-widevine-license-template-overview.md)
### [DRM-licensleverans](media-services-deliver-keys-and-licenses.md)
### [Använda partner för att leverera Widevine-licenser till Media Services](media-services-licenses-partner-integration.md)
### [Använda PlayReady och/eller Widevine Dynamic Common Encryption](media-services-protect-with-drm.md)
### [Strömma HLS-innehåll som skyddats med Apple FairPlay](media-services-protect-hls-with-fairplay.md)
### [CENC med Multi-DRM och Access Control](media-services-cenc-with-multidrm-access-control.md)
### [Använd Axinom för att leverera Widevine-licenser till Media Services](media-services-axinom-integration.md)
### [Använd castLabs för att leverera Widevine-licenser till Media Services](media-services-castlabs-integration.md)

### Tillgångsleverans
#### Konfigurera tillgångsleveransprinciper
##### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
##### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Skapa ContentKeys
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Konfigurera principen för auktorisering av innehållsnycklar
#### [Portal](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)

## [Analysera](media-services-analytics-overview.md)
### [Analysera media med portalen](media-services-portal-analyze.md)
### [Bearbeta med Indexer 2](media-services-process-content-with-indexer2.md)
### [Bearbeta med Indexer](media-services-index-content.md)
### [Bearbeta med videostabilisering](media-services-hyperlapse-content.md)
### [Bearbeta med ansiktsigenkänning](media-services-face-and-emotion-detection.md)
### [Bearbeta med rörelseidentifiering](media-services-motion-detection.md)
### [Bearbeta med ansiktsredigering](media-services-face-redaction.md)
#### [Genomgång av ansiktsredigering](media-services-redactor-walkthrough.md)
### [Bearbeta med videominiatyrer](media-services-video-summarization.md)
### [Bearbeta med OCR](media-services-video-optical-character-recognition.md)

## [Konfigurera telemetri](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST](media-services-rest-telemetry.md)

## Skala
### [Mediebearbetning](media-services-scale-media-processing-overview.md)
#### [Portal](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
#### [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
### Slutpunkter för direktuppspelning
#### [Portal](media-services-portal-scale-streaming-endpoints.md)

## [Leverera innehåll](media-services-deliver-content-overview.md)
### [Dynamisk paketering](media-services-dynamic-packaging-overview.md)
### [Översikt över filter och dynamiska manifest](media-services-dynamic-manifest-overview.md)
#### [Skapa filter med .NET](media-services-dotnet-dynamic-manifest.md)
#### [Skapa filter med REST](media-services-rest-dynamic-manifest.md)
### [CDN-cachelagringsprincip i Media Services-tillägget](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### Publicera innehåll
#### [Portal](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [Leverera efter hämtning](media-services-deliver-asset-download.md)
### [Scenario vid direktuppspelningsredundans](media-services-implement-failover.md)

## Förbruka
### [Spela upp media med befintliga spelare](media-services-playback-content-with-existing-players.md)
### [Spela upp media med Media Player](media-services-develop-video-players.md)
### Andra uppspelningsalternativ
#### [Jämn direktuppspelning av Windows Store-program](media-services-build-smooth-streaming-apps.md)
#### [HTML5-program med DASH.js](media-services-embed-mpeg-dash-in-html5.md)
#### [Adobe Open Source Media Framework-spelare](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [Infoga annonser på klientsidan](media-services-inserting-ads-on-client-side.md)
### [Licensiera Microsoft Smooth Streaming-klientportningskit](media-services-sspk.md)

## Integrera
### [Använd Azure Functions med Media Services](media-services-dotnet-how-to-use-azure-functions.md)
### [Exempel på Azure Functions med Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## Övervaka
### Kontrollera jobbförlopp
#### [REST](media-services-rest-check-job-progress.md)
#### [Portal](media-services-portal-check-job-progress.md)
#### [NET](media-services-check-job-progress.md)
### [Övervaka jobbaviseringar med kölagring](media-services-dotnet-check-job-progress-with-queues.md)
### [Övervaka jobbaviseringar med webhookar](media-services-dotnet-check-job-progress-with-webhooks.md)

## Felsöka
### [Vanliga frågor och svar](media-services-frequently-asked-questions.md)
### [Felsökningsguide för liveuppspelning](media-services-troubleshooting-live-streaming.md)
### [Felkoder](media-services-error-codes.md)
### [Logik för omprövning](media-services-retry-logic-in-dotnet-sdk.md)

# Referens
## [PowerShell (Resource Manager)](/powershell/resourcemanager/azurerm.media/v0.3.1/azurerm.media)
## [PowerShell (Service Management)](/powershell/servicemanagement/azure.compute/v3.1.0/azure.compute)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](/rest/api/media)

# Resurser
## [Viktig information](media-services-release-notes.md)
## [Prissättning](https://azure.microsoft.com/pricing/details/media-services/)
## [Azure Media Services-community](media-services-community.md)
## [Videoklipp](https://azure.microsoft.com/resources/videos/index/?services=media-services)
