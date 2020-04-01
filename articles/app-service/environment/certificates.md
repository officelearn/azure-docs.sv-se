---
title: Certifikatbindningar
description: Förklara många ämnen som rör certifikat i en App Service-miljö. Läs om hur certifikatbindningar fungerar på appar med en enda klientorganisation i en ASE.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ba1d06ce83d50b6f0db84d1e423e66eae98f665d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477510"
---
# <a name="certificates-and-the-app-service-environment"></a>Certifikat och apptjänstmiljön 

App Service Environment(ASE) är en distribution av Azure App Service som körs inom ditt Virtuella Azure-nätverk(VNet). Den kan distribueras med en internetanpassningstillgänglig programslutpunkt eller en programslutpunkt som finns i ditt virtuella nätverk. Om du distribuerar ASE med en internettillgänglig slutpunkt kallas den distributionen för extern ASE. Om du distribuerar ASE med en slutpunkt i ditt virtuella nätverk kallas den distributionen för ILB ASE. Du kan läsa mer om ILB ASE från [Skapa och använda ett ILB ASE-dokument.](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)

ASE är ett enda klientsystem. Eftersom det är en enda klient finns det vissa funktioner som endast är tillgängliga med en ASE som inte är tillgängliga i Apptjänsten för flera innehavare. 

## <a name="ilb-ase-certificates"></a>ILB ASE-certifikat 

Om du använder en extern ASE nås dina appar på [appnamn]. [asename].p.azurewebsites.net. Som standard skapas alla ASEs, även ILB ASEs, med certifikat som följer det formatet. När du har en ILB ASE nås apparna baserat på det domännamn som du anger när du skapar ILB ASE. För att apparna ska stödja TLS måste du ladda upp certifikat. Skaffa ett giltigt TLS/SSL-certifikat med hjälp av interna certifikatutfärdare, köpa ett certifikat från en extern utfärdare eller använda ett självsignerat certifikat. 

Det finns två alternativ för att konfigurera certifikat med din ILB ASE.  Du kan ange ett jokerteckenstandardcertifikat för ILB ASE eller ange certifikat på de enskilda webbapparna i ASE.  Oavsett vilket val du väljer måste följande certifikatattribut konfigureras korrekt:

- **Angående:** Det här attributet måste anges till *. [din-root-domain-here] för ett jokertecken ILB ASE-certifikat. Om du skapar certifikatet för din app bör det vara [appnamn]. [din-root-domän-här]
- **Ämne alternativt namn:** Det här attributet måste innehålla båda *. [din-root-domän-domän-här] och *.scm. [din-root-domain-here] för jokertecken ILB ASE-certifikatet. Om du skapar certifikatet för din app bör det vara [appnamn]. [din-root-domain-here] och [appname].scm. [din-root-domän-här].

Som en tredje variant kan du skapa ett ILB ASE-certifikat som innehåller alla dina enskilda appnamn i CERTIFIKATET i certifikatet i stället för att använda en jokerteckenreferens. Problemet med den här metoden är att du måste veta på framsidan namnen på de appar som du lägger i ASE eller du behöver för att hålla uppdatera ILB ASE-certifikat.

### <a name="upload-certificate-to-ilb-ase"></a>Ladda upp certifikat till ILB ASE 

När en ILB ASE har skapats i portalen måste certifikatet anges för ILB ASE. Tills certifikatet har angetts visar ASE en banderoll som certifikatet inte har angetts.  

Certifikatet som du överför måste vara en PFX-fil. När certifikatet har överförts utför ASE en skalningsåtgärd för att ange certifikatet. 

Du kan inte skapa ASE och överföra certifikatet som en åtgärd i portalen eller ens i en mall. Som en separat åtgärd kan du ladda upp certifikatet med hjälp av en mall enligt beskrivningen i [Skapa en ASE från ett malldokument.](./create-from-template.md)  

Om du snabbt vill skapa ett självsignerat certifikat för testning kan du använda följande bit powershell:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
När du skapar ett självsignerat certifikat måste du se till att ämnesnamnet har formatet CN={ASE_NAME_HERE}_InternalLoadBalancingASE.

## <a name="application-certificates"></a>Programcertifikat 

Appar som finns i en ASE kan använda de appcentrerade certifikatfunktioner som är tillgängliga i Apptjänsten för flera innehavare. Dessa funktioner inkluderar:  

- SNI-certifikat 
- IP-baserad SSL, som endast stöds med en extern ASE.  En ILB ASE stöder inte IP-baserad SSL.
- KeyVault värdcertifikat 

Instruktionerna för att ladda upp och hantera dessa certifikat finns i [Lägg till ett TLS/SSL-certifikat i Azure App Service](../configure-ssl-certificate.md).  Om du bara konfigurerar certifikat så att de matchar ett eget domännamn som du har tilldelat din webbapp räcker dessa instruktioner. Om du laddar upp certifikatet för en ILB ASE-webbapp med standarddomännamnet anger du scm-platsen i CERTIFIKATET för certifikatet som tidigare nämnts. 

## <a name="tls-settings"></a>TLS-inställningar 

Du kan konfigurera TLS-inställningen på appnivå.  

## <a name="private-client-certificate"></a>Privat klientcertifikat 

Ett vanligt användningsfall är att konfigurera appen som klient i en klient-servermodell. Om du skyddar servern med ett privat certifikatutfärdarcertifikat måste du ladda upp klientcertifikatet till din app.  Följande instruktioner läser in certifikat till truststore för de arbetare som appen körs på. Om du läser in certifikatet i en app kan du använda det med dina andra appar i samma App Service-plan utan att ladda upp certifikatet igen.

Så här laddar du upp certifikatet till din app i DIN ASE:

1. Generera en *CER-fil* för ditt certifikat. 
2. Gå till appen som behöver certifikatet i Azure-portalen
3. Gå till SSL-inställningar i appen. Klicka på Ladda upp certifikat. Välj Offentlig. Välj Lokal dator. Ange ett namn. Bläddra och välj *fil i .cer.* Välj uppladdning. 
4. Kopiera tumavtrycket.
5. Gå till Programinställningar. Skapa en appinställning WEBSITE_LOAD_ROOT_CERTIFICATES med tumavtrycket som värde. Om du har flera certifikat kan du placera dem i samma inställning åtskilda av kommatecken och inget blanktecken som 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Certifikatet kommer att vara tillgängligt för alla appar i samma apptjänstplan som appen, som konfigurerade den inställningen. Om du behöver den för att vara tillgänglig för appar i en annan App Service-plan måste du upprepa appinställningsåtgärden i en app i apptjänstplanen. Om du vill kontrollera att certifikatet är inställt går du till Kudu-konsolen och utfärdar följande kommando i PowerShell-felsökningskonsolen:

    dir cert:\localmachine\root

Om du vill utföra testning kan du skapa ett självsignerat certifikat och generera en *CER-fil* med följande PowerShell: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

