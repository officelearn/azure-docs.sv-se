---
title: Felsöka roller som inte startar | Microsoft-dokument
description: Här är några vanliga orsaker till att en molntjänstroll inte kan starta. Lösningar på dessa problem finns också.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 869453d92f536a62aacc2be52598223158566ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122732"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Felsöka molntjänstroller som inte startar
Här är några vanliga problem och lösningar relaterade till Azure Cloud Services-roller som inte startar.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLL-filer eller beroenden saknas
Om du inte svarar på roller och roller som är i närheten av **initiering,** **upptagen**och **stoppa** kan tillstånden saknas av DLL-filer eller sammansättningar.

Symptom på saknade DLL-filer eller sammansättningar kan vara:

* Din rollförekomst är cykling genom **initiera,** **upptagen**och **stoppa** tillstånd.
* Rollinstansen har flyttats till **Klar,** men om du navigerar till webbprogrammet visas inte sidan.

Det finns flera rekommenderade metoder för att undersöka dessa problem.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostisera saknade DLL-problem i en webbroll
När du navigerar till en webbplats som distribueras i en webbroll och webbläsaren visar ett serverfel som liknar följande, kan det tyda på att en DLL saknas.

![Serverfel i /'-programmet.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostisera problem genom att inaktivera anpassade fel
Mer fullständig felinformation kan visas genom att konfigurera web.config för webbrollen för att ställa in det anpassade felläget till Av och distribuera om tjänsten.

Så här visar du fler fullständiga fel utan att använda Fjärrskrivbord:

1. Öppna lösningen i Microsoft Visual Studio.
2. Leta reda på filen web.config i **Solution Explorer**och öppna den.
3. Leta upp avsnittet system.web i filen web.config och lägg till följande rad:

    ```xml
    <customErrors mode="Off" />
    ```
4. Spara filen.
5. Packa om och distribuera om tjänsten.

När tjänsten har distribuerats om visas ett felmeddelande med namnet på den saknade sammansättningen eller DLL-filen.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostisera problem genom att visa felet på distans
Du kan använda Fjärrskrivbord för att komma åt rollen och visa mer fullständig felinformation på distans. Gör så här om du vill visa felen med fjärrskrivbord:

1. Kontrollera att Azure SDK 1.3 eller senare är installerat.
2. Aktivera Fjärrskrivbord under distributionen av lösningen med Visual Studio. Mer information finns i [Aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. I Microsoft Azure-portalen, när instansen visar en status **för Ready**, fjärrkontroll till instansen. Mer information om hur du använder fjärrskrivbordet med Molntjänster finns [i Fjärr till rollinstanser](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Logga in på den virtuella datorn med hjälp av de autentiseringsuppgifter som angavs under konfigurationen för fjärrskrivbord.
6. Öppna ett kommandofönster.
7. Skriv `IPconfig`.
8. Observera värdet för IPV4-adress.
9. Öppna Internet Explorer.
10. Skriv adressen och namnet på webbprogrammet. Till exempel `http://<IPV4 Address>/default.aspx`.

Om du navigerar till webbplatsen returneras nu tydligare felmeddelanden:

* Serverfel i /'-programmet.
* Beskrivning: Ett ohanterat undantag inträffade under körningen av den aktuella webbbegäran. Läs stackspårningen för mer information om felet och var det har sitt ursprung i koden.
* Undantagsinformation: System.IO.FIleNotFoundException: Det gick inte att läsa in filen eller sammansättningen Microsoft.WindowsAzure.StorageClient, Version=1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35 eller något av dess beroenden. Det går inte att hitta den angivna filen.

Ett exempel:

![Explicit serverfel i /-programmet](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostisera problem med hjälp av beräkningsemulatorn
Du kan använda Microsoft Azure-beräkningsemulatorn för att diagnostisera och felsöka problem med saknade beroenden och web.config-fel.

För bästa resultat när du använder den här diagnosmetoden bör du använda en dator eller virtuell dator som har en ren installation av Windows. Använd Windows Server 2008 R2 x64 för att simulera Azure-miljön på bästa sätt.

1. Installera den fristående versionen av [Azure SDK](https://azure.microsoft.com/downloads/).
2. På utvecklingsmaskinen skapar du molntjänstprojektet.
3. I Utforskaren navigerar du till mappen bin\debug i molntjänstprojektet.
4. Kopiera CSX-mappen och CSCFG-filen till den dator som du använder för att felsöka problemen.
5. Öppna ett Kommandomeddelandefönster för Azure SDK `csrun.exe /devstore:start`på den rena datorn och skriv .
6. Skriv i `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`kommandotolken .
7. När rollen startar visas detaljerad felinformation i Internet Explorer. Du kan också använda standardverktyg för felsökning av Windows för att ytterligare diagnostisera problemet.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostisera problem med intelliTrace
För arbets- och webbroller som använder .NET Framework 4 kan du använda [IntelliTrace](/visualstudio/debugger/intellitrace), som är tillgängligt i Microsoft Visual Studio Enterprise.

Så här distribuerar du tjänsten med IntelliTrace aktiverat:

1. Bekräfta att Azure SDK 1.3 eller senare är installerat.
2. Distribuera lösningen med Visual Studio. Under distributionen markerar du kryssrutan **Aktivera IntelliTrace för .NET 4-roller.**
3. När instansen startar öppnar du **Server Explorer**.
4. Expandera **Azure\\Cloud Services-noden** och hitta distributionen.
5. Expandera distributionen tills du ser rollinstanserna. Högerklicka på en av instanserna.
6. Välj **Visa intelliTrace-loggar**. **IntelliTrace-sammanfattningen** öppnas.
7. Leta reda på undantagsavsnittet i sammanfattningen. Om det finns undantag kommer avsnittet att märkas **undantagsdata**.
8. Expandera **undantagsdata** och leta efter **System.IO.FileNotFoundException-fel** som liknar följande:

![Undantagsdata, fil som saknas eller sammansättning](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Adress saknas DLL-filer och sammansättningar
Så här åtgärdar du DLL- och monteringsfel som saknas:

1. Öppna lösningen i Visual Studio.
2. Öppna mappen **Referenser** i **Solution Explorer.**
3. Klicka på den sammansättning som identifierades i felet.
4. Leta reda på **egenskapen Kopiera lokalt** i egenskapen **Egenskaper** och ange värdet till **Sant**.
5. Distribuera om molntjänsten.

När du har kontrollerat att alla fel har korrigerats kan du distribuera tjänsten utan att markera kryssrutan **Aktivera IntelliTrace för .NET 4-roller.**

## <a name="next-steps"></a>Nästa steg
Visa fler [felsökningsartiklar](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) för molntjänster.

Mer information om hur du felsöker problem med molntjänstroll med hjälp av Azure PaaS datordiagnostikdata finns i [Kevin Williamsons bloggserie](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
