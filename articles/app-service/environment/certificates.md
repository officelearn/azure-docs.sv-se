---
title: Certifikat och App Service Environment - Azure
description: Förklara många ämnen som rör certifikat på en ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bcb0c806d916b9dff4461cad829a1d75e8df7cf6
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271903"
---
# <a name="certificates-and-the-app-service-environment"></a>Certifikat och App Service-miljö 

App Service-miljö(ase) är en distribution av Azure App Service som körs i din Azure virtuellt nätverk (vnet). Den kan distribueras med en internet-slutpunkt för tillgängliga program eller en programslutpunkt som är i ditt virtuella nätverk. Om du distribuerar ASE med en internet-tillgänglig slutpunkt kallas en extern ASE för distributionen. Om du distribuerar ASE med en slutpunkt i ditt virtuella nätverk kallas en ILB ASE med distributionen. Du kan lära dig mer om ILB ASE från den [skapa och använda en ILB ASE](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase) dokumentet.

ASE är ett system med enskild klient. Eftersom det är en enskild klient, finns det vissa funktioner endast med en ASE som inte är tillgängliga i App Service med flera innehavare. 

## <a name="ilb-ase-certificates"></a>ILB ASE-certifikat 

Om du använder en extern ASE kan nå dina appar just [programnamn]. [asename]. p.azurewebsites.net. Som standard skapas alla ase-miljöer, även ILB ase, med certifikat som följer formatet. När du har en ILB ASE, nås apparna baserat på det domännamn som du anger när du skapar ILB ASE. Du måste ladda upp certifikat för appar som stöd för SSL. Skaffa ett giltigt SSL-certifikat med hjälp av interna certifikatutfärdare, köp ett certifikat från en extern utfärdare eller använder ett självsignerat certifikat. 

Det finns två alternativ för att konfigurera certifikat med din ILB ASE.  Du kan ange ett jokerteckencertifikat standard för ILB ASE eller ställa in certifikat på de enskilda web apps i ase-miljön.  Följande certifikatattribut måste konfigureras korrekt oavsett de val du gör:

- **Ämne:** Det här attributet måste anges till *. [your-root-domain-here] för ett jokerteckencertifikat för ILB ASE. Om du skapar certifikatet för din app ska [programnamn]. [your-root-domain-here]
- **Alternativt namn för certifikatmottagare:** Det här attributet måste innehålla både *. [your-root-domain-here] och *.scm. [your-root-domain-here] för ILB ASE jokerteckencertifikat. Om du skapar certifikatet för din app ska [programnamn]. [your-root-domain-here] och [programnamn] .scm. [your-root-domain-here].

Du kan skapa en ILB ASE-certifikat som innehåller alla dina enskilda appnamn i SAN för certifikatet istället för att använda en referens som en tredje variant. Problem med den här metoden är att du behöver veta direkt namnen på de appar som du placerar i ASE eller måste du fortsätter att uppdatera ILB ASE-certifikatet.

### <a name="upload-certificate-to-ilb-ase"></a>Överför certifikatet till ILB ASE 

När en ILB ASE har skapats i portalen, måste du ange certifikatet för ILB ASE. Tills certifikatet har angetts, visar ASE en banderoll att certifikatet inte har angetts.  

Certifikatet som du överför måste vara en .pfx-fil. När certifikatet har överförts utför ASE en skalningsåtgärd för att ange certifikatet. 

Du kan inte skapa ASE och ladda upp certifikatet som en åtgärd i portalen eller även i en mall. Som en separat åtgärd kan du ladda upp det certifikat som använder en mall som beskrivs i den [skapa ASE från en mall](./create-from-template.md) dokumentet.  

Om du vill skapa ett självsignerat certifikat för att snabbt testa använder du följande PowerShell-bitars:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     


## <a name="application-certificates"></a>Programcertifikat 

Appar som finns i en ASE kan använda app-centric certifikat-funktioner som är tillgängliga i App Service med flera innehavare. Dessa funktioner:  

- SNI-certifikat 
- IP-baserad SSL, som stöds endast med en extern ASE.  En ILB ASE har inte stöd för IP-baserad SSL.
- KeyVault finns certifikat 

Instruktioner för att ladda upp och hantera dessa certifikat finns i självstudien för App Service SSL https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl.  Om du bara konfigurerar certifikat så att det matchar ett anpassat domännamn som du har tilldelat till din webbapp, räcker dessa instruktioner. Om du laddar upp certifikatet för en ILB ASE-webbapp med standarddomännamnet, anger du sedan scm-webbplatsen i SAN för certifikatet som du antecknade tidigare. 

## <a name="tls-settings"></a>TLS-inställningar 

Du kan konfigurera TLS-inställningen för en app-nivå.  

## <a name="private-client-certificate"></a>Privata klientcertifikat 

Ett vanligt användningsfall är att konfigurera din app som en klient i en klient / server-modell. Om du skyddar servern med ett privata CA-certifikat, behöver du överföra klientcertifikatet till din app.  Följande anvisningar läser in certifikat till truststore arbetstagarnas kör din app. Om du läser in certifikat till en app, kan du använda den med dina andra appar i samma App Service-plan utan att överföra certifikatet igen.

Ladda upp certifikatet till din app i din ASE:

1. Generera en *.cer* -filen för certifikatet. 
2. Gå till den app som behöver få certifikatet i Azure portal
3. Gå till SSL-inställningar i appen. Klicka på Överför certifikat. Välj offentliga. Välj lokal dator. Ange ett namn. Bläddra och välj din *.cer* fil. Välj överför. 
4. Kopiera tumavtrycket.
5. Gå till inställningar för program. Skapa en App inställningen WEBSITE_LOAD_ROOT_CERTIFICATES med tumavtryck som värde. Om du har flera certifikat, kan du placera dem i samma inställning avgränsade med kommatecken och inga blanksteg som 

    84EC242A4EC7957817B8E48913E50953552DAFA6 6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Certifikatet kan användas av alla appar i samma app service-plan som appen som den här inställningen har konfigurerats. Om du behöver den ska vara tillgänglig för appar i en annan App Service-plan kan behöver du upprepa Appinställningen åtgärden i en app i den här App Service-planen. Om du vill kontrollera att certifikatet har angetts, går du till Kudu-konsolen och utfärda den här annan kommandot dir cert: \localmachine\root i PowerShell-Felsökningskonsolen. 

Om du vill utföra testning, kan du skapa ett självsignerat certifikat och generera en *.cer* fil med följande PowerShell: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

