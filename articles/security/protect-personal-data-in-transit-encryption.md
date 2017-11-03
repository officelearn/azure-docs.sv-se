---
title: "Skydda personliga data under överföringen med kryptering i Azure | Microsoft Docs"
description: "Använder kryptering i Azure för att skydda personliga data"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 461ddfda796bfe6639e27f6c4cd53e82b4e397d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>Azure krypteringstekniker: skydda personliga data under överföringen med kryptering

Den här artikeln hjälper dig att förstå och använda Azure krypteringstekniker att skydda data under överföringen. 

Skydd av personliga data över nätverket är en viktig del av en flera lager skydd på djupet säkerhetsstrategi. Kryptering under överföring är utformat för att hindra en angripare fångar upp överföringar från att visa eller använda data.

## <a name="scenario"></a>Scenario

Ett stort kryssning företag, sitt säte i USA utökar åtgärderna för att erbjuda färdvägar i Medelhavet, Adriatiska havet, baltiska havet samt Förenta staterna. För att stödja dessa ansträngningar genererade den flera mindre kryssning rader i Italien, Tyskland, Danmark och Storbritannien 

Företaget använder Microsoft Azure för att lagra företagets data i molnet. Detta inkluderar personligt identifierbar information, till exempel namn, adresser, telefonnummer och kreditkortsinformation av dess globala kundbas. Den innehåller också traditionella personal information, till exempel adresser, telefonnummer, skatt ID-nummer och annan information om företagets anställda på alla platser. Raden kryssning har också en stor databas med medlemmar av trafik och förmåner som innehåller personuppgifter för att spåra relationer med aktuella och tidigare kunder.

Personliga data för kunder som har angetts i databasen från företagets fjärranslutna kontor och från resa agenter finns runtom i världen. Dokument som innehåller kundinformation överförs via nätverket till Azure-lagring.

## <a name="problem-statement"></a>Problembeskrivning

Företaget måste skydda kundernas och anställdas personliga data när den är i överföringen till och från Azure-tjänster.

## <a name="company-goal"></a>Företagets mål

Företagets målet så att personliga data krypteras när av disken. Om obehöriga avlyssna av disk personliga data, måste den vara i ett formulär som kommer att visas inte kan läsas. Tillämpa kryptering ska vara enkelt eller helt transparent för användare och administratörer.

## <a name="solutions"></a>Lösningar

Azure-tjänster ger flera verktyg och tekniker som hjälper dig att skydda personliga data under överföringen.

### <a name="azure-storage"></a>Azure Storage

Data som lagras i molnet måste åka till klienten och som kan vara fysiskt finns var som helst i världen, till Azure-datacentret. När dessa data hämtas av användare, flyttar du det igen i motsatt riktning. Data som är under överföring via det offentliga Internet är alltid i fara för avlyssning av angripare. Det är viktigt att skydda personliga data med hjälp av kryptering på transportnivå för att skydda den som flyttas mellan platser.

HTTPS-protokollet ger en säker och krypterad kommunikationskanal via Internet. HTTPS ska användas för åtkomst till objekt i Azure Storage och vid anrop av REST API: er. Du kan framtvinga användning av HTTPS-protokollet när du använder [signaturer för delad åtkomst](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) (SAS) för att delegera åtkomst till Azure Storage-objekt. Det finns två typer av SAS: tjänst-SAS och konto-SAS.

#### <a name="how-do-i-construct-a-service-sas"></a>Hur jag för att skapa en tjänst-SAS?

En tjänst-SAS delegerar åtkomst till en resurs i en av lagringstjänsterna (blob, kö-, tabell- eller filen service). Om du vill skapa en tjänst-SAS gör du följande:

1. Ange fältet signerade Version

2. Ange den signera resursen (Blob och fil-tjänsten)

3. Ange frågeparametrar om du vill åsidosätta svarshuvuden (Blob-tjänsten och fil-tjänsten)

4. Ange tabellnamnet på (endast Tabelltjänsten)

5. Ange åtkomstprincipen

6. Ange giltighetsintervall signatur

8. Ange behörigheter

9. Ange IP-adress eller IP-intervall

10. Ange HTTP-protokollet

11. Ange intervall för åtkomst av tabell

12. Ange den signerade identifieraren

13. Ange signaturen

Mer instruktioner finns [hur du skapar en tjänst-SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN).

#### <a name="how-do-i-construct-an-account-sas"></a>Hur jag för att skapa ett konto-SAS?

En konto-SAS delegerar åtkomst till resurser i en eller flera av lagringstjänsterna. Du kan också delegera åtkomst till läs-, skriv- och borttagningsåtgärder i blobbbehållare, tabeller, köer och filresurser som inte tillåts med en tjänst-SAS. Skapa ett konto-SAS liknar som en tjänst-SAS. Detaljerade instruktioner finns [hur du skapar ett konto-SAS.](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN)

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>Hur behöver jag använda HTTPS vid anrop av REST API: er?

Om du vill framtvinga användning av HTTPS vid anrop av REST API: er för åtkomst till objekt i storage-konton, kan du aktivera säker överföring krävs för lagringskontot. 

1. Välj i Azure-portalen **skapa Lagringskonto**, eller ett befintligt lagringskonto, Välj **inställningar** och sedan **Configuration**.

2. Under **säker överföring krävs**väljer **aktiverad**.

![Skapa ett lagringskonto](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

Detaljerade instruktioner, inklusive hur du ger säker överföring krävs programmässigt, se [kräver säker överföring](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer).

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>Hur jag för att kryptera data i Azure File Storage?

Kryptera data under överföring med [Azure File Storage](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal), du kan använda SMB 3.x med Windows 8, 8.1 och 10 och Windows Server 2012 R2 och Windows Server 2016. När du använder tjänsten Azure-filer, misslyckas en anslutning utan kryptering när ”säker överföring krävs” är aktiverat. Detta inkluderar scenarier med hjälp av SMB 2.1 och SMB 3.0 utan kryptering vissa varianter av Linux SMB-klienten.

#### <a name="azure-client-side-encryption"></a>Azure klientsidan kryptering

Ett annat alternativ för att skydda personliga data medan de överförs mellan ett klientprogram och Azure Storage är [Client side Encryption](https://docs.microsoft.com/azure/storage/storage-client-side-encryption). Data krypteras innan de överförs till Azure Storage och när du hämtar data från Azure Storage data dekrypteras när den tas emot på klientsidan.

### <a name="azure-site-to-site-vpn"></a>Azure VPN för plats-till-plats

Ett effektivt sätt att skydda personliga data som överförs mellan ett företagsnätverk eller användare och virtuella Azure-nätverket är att använda en [plats-till-plats](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) eller [punkt-till-plats](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) virtuella privata nätverk (VPN). En VPN-anslutning skapar en säker krypterad tunnel via Internet.

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>Hur skapar jag en plats-till-plats VPN-anslutning

En plats-till-plats-VPN ansluter flera användare på företagets nätverk till Azure. Om du vill skapa en plats-till-plats-anslutning i Azure-portalen, gör du följande:

1. Skapa ett virtuellt nätverk.

2. Ange en DNS-server.

3. Skapa gateway-undernätet.

4. Skapa VPN-gatewayen. 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. Skapa lokal nätverksgateway.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. Konfigurera din VPN-enhet.

7. Skapa VPN-anslutningen.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. Kontrollera VPN-anslutningen.

Mer detaljerad information om hur du skapar en plats-till-plats-anslutning i Azure portal finns i [skapa en plats-till-plats-anslutning i Azure Portal.] (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>Hur skapar jag en punkt-till-plats VPN-anslutning

En punkt-till-plats-VPN skapar en säker anslutning från en enskild klientdator till ett virtuellt nätverk. Detta är användbart när du vill ansluta till Azure från en fjärrdator som hemma eller ett hotell eller konferens Center. Att skapa en punkt-till-plats-anslutning i Azure-portalen

1. Skapa ett virtuellt nätverk.

2. Lägg till en gateway-undernätet.

3. Ange en DNS-server. (valfritt)

4. Skapa en virtuell nätverksgateway.

5. Generera certifikat.

6. Lägg till klient-adresspool.

7. Ladda upp roten certifikat certifikatets offentliga data.

8. Generera och installera VPN-klientpaketet för konfigurationen.

9. Installera ett exporterade klientcertifikat.

10. Ansluta till Azure.

11. Verifiera anslutningen.

Mer instruktioner finns [konfigurerar en punkt-till-plats-anslutning till ett virtuellt nätverk som använder certifikatautentisering för: Azure-portalen.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)

### <a name="ssltls"></a>SSL/TLS

Microsoft rekommenderar att du alltid använder SSL/TLS-protokoll för att utbyta data mellan olika platser. Organisationer som använder [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) länk kan flytta stora datamängder via ett dedikerat snabba WAN också kryptera data på programnivå-med hjälp av SSL/TLS- eller andra protokoll för skydd.

### <a name="encryption-by-default"></a>Kryptering som standard

Microsoft använder kryptering för att skydda data under överföring mellan kunder och Azure-molntjänster. Om du interagerar med Azure Storage via Azure Portal, alla transaktioner ska ske via HTTPS.

[Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) är det protokoll som Microsoft-datacenter försöker att förhandla med klientdatorer som ansluter till Microsofts molntjänster. TLS innehåller stark autentisering, meddelandesekretess, och integritet (aktiveras identifiering av meddelandet manipulation avlyssning och förfalskning), samverkan, flexibla algoritmer, enkel distribution och användning.

[Perfekt Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (Secrecy) används också så att varje anslutning mellan Microsofts molntjänster och kunders klientsystem använda unika nycklar. Anslutningar till Microsoft-molntjänster också dra nytta av RSA baserat 2 048-bitarskryptering nyckellängder. Kombinationen av TLS, RSA 2 048-bitars nyckellängd, och PFS gör det mycket svårare för andra att snappa upp och komma åt data som överförs mellan Microsoft-molntjänster och kunder.

Det krypteras alltid data under överföring i [Data Lake Store] (https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview). Förutom att kryptera data före lagring till permanenta media skyddas alltid även data under överföring eller i rörelse med hjälp av HTTPS. HTTPS är det enda protokoll som stöds för Data Lake Store REST-gränssnitt.

## <a name="summary"></a>Sammanfattning

Företaget kan göra syftet med att skydda personliga data och sekretessen för sådana data genom att använda HTTPS-anslutningar till Azure Storage, använder signaturer för delad åtkomst och att aktivera säker överföring krävs på storage-konton. De kan även skydda personliga data med hjälp av SMB 3.0-anslutningar och implementera kryptering på klientsidan. Plats-till-plats VPN-anslutningar från företagsnätverket till virtuella Azure-nätverket och punkt-till-plats VPN-anslutningar från enskilda användare skapar en säker tunnel som personliga data på ett säkert sätt kan överföras. Microsofts standard kryptering praxis att ytterligare skydda personliga data.

## <a name="next-steps"></a>Nästa steg

- [Metodtips för säkerhet för Azure Data och kryptering](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [Planering och design för VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [Vanliga frågor och svar om VPN-gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [Köpa och konfigurera ett SSL-certifikat för Azure App Service](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)
