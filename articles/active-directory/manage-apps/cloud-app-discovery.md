---
title: Konfigurera Cloud App Discovery-tjänsten i Azure Active Directory | Microsoft Docs
description: Hitta och hantera program med Cloud App Discovery genom att ge tillgång till rätt information om molnanvändning och shadow IT.
services: active-directory
keywords: cloud app discovery, hantera program
documentationcenter: ''
author: barbkess
manager: mtillman
tags: ignite
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 04/25/2018
ms.author: barbkess
ms.reviewer: nigu
ms.openlocfilehash: 0b1c525ea0333d91538986810981eee10d1c4f2a
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Konfigurera Cloud App Discovery i Azure AD

Cloud App Discovery i Azure AD är nu baserat på integrering med data som är tillgängliga från Microsoft Cloud App Security. Om du vill ge pågående information om molnanvändning och shadow IT, jämför Cloud App Discovery dina trafikloggar till Cloud App Security-katalog med över 15 000 molnappar. Den här artikeln beskriver installationsprocessen och innehåller länkar till detaljerad information om varje steg. Här beskrivs också brandväggar och proxyservrar information och logga stöd.

## <a name="prerequisites"></a>Förutsättningar

* Din organisation måste ha en Azure AD Premium P1-licens för att använda produkten. Mer information finns i [priser för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
* Du måste vara en Global administratör eller en läsare för säkerhet i Azure Active Directory om du vill konfigurera Cloud App Discovery.

## <a name="setup-steps"></a>Konfigurationssteg

1. [Ställa in ögonblicksbild rapporter](cloud-app-discovery-create-snapshot-reports.md) för att kontrollera ditt loggformat Kontrollera loggarna innehåller användbar information till Cloud App Discovery. De kan också ge ad hoc-insyn i trafikloggar ladda upp manuellt från dina brandväggar och proxyservrar.

2. [Ställ in kontinuerlig reporting](https://docs.microsoft.com/cloud-app-security/discovery-docker) att analysera alla loggar som vidarebefordras från nätverket med hjälp av logginsamlare Cloud App Security. Du kan använda dem för att identifiera nya appar och användningstrender.

3. Om dina loggar inte stöds för närvarande, [ställa in en anpassad logg-parser](https://docs.microsoft.com/cloud-app-security/custom-log-parser) så att Cloud App Discovery kan analysera dem.
  
## <a name="log-processing-flow"></a>Loggen processchema

Det kan ta allt från några minuter till flera timmar att generera rapporter beroende på mängden data. Här är vad analyseras:

* **Överför** loggar med webbtrafik från nätverket upp till portalen.
* **Parsa** Cloud App Security tolkar och extraherar trafikdata från trafikloggarna med en dedikerad parser för varje datakälla.
* **Analysera** trafikinformationen analyseras mot Cloud App Security-katalogen om du vill identifiera mer än 15 000 molnappar. Aktiva användare och IP-adresser identifieras också som en del av analysen.
* **Generera en rapport** Cloud App Security genererar en rapport med data som hämtats från loggfiler och gör den tillgänglig för Cloud App Discovery.

> [!NOTE]
> * Kontinuerlig rapportdata analyseras två gånger om dagen.
> * Logginsamlaren komprimerar data innan det överförs. Utgående trafik på logginsamlaren är cirka 10% av de mottagna trafikloggarna storlek.

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Med hjälp av trafikloggar för Cloud App Discovery

Cloud App Discovery använder data i dina trafikloggar. Mer information om du kan lägga till i loggen, du får bättre synlighet. Cloud App Discovery kräver webbtrafik data med följande attribut:

* Datum för transaktionen
* Källans IP-adress
* Källanvändare **rekommenderas**
* Mål-IP-adress
* Mål-URL **rekommenderas** (URL: er ger mer exakt för molnappar än IP-adresser)
* Total mängd data
* Mängden laddat upp eller nedladdade data efter insikter om mönster för app-molnanvändningen
* Åtgärd (tillåtna/blockerade)

Cloud App Discovery kan inte visa eller analysera attribut som inte ingår i loggarna. Till exempel **Cisco ASA-brandväggar** standard loggformat innehåller inte den **överförda byte per transaktion**, **användarnamn**, eller **mål-URL**  men bara den IP-adressen. Därför kanske du har mindre inblick i molnappar från den här datakällan. Ange information till 6.1 för Cisco ASA-brandväggar.

För att kunna generera en rapport för Cloud App Discovery, måste dina trafikloggar uppfylla följande villkor:

1.  Datakällan är [en brandvägg eller proxyserver server](#supported-firewalls-and-proxies).
2.  Loggformat matchar det förväntade formatet som standard. Den är markerad i tid. För att optimera uour loggfilsformat finns [Skapa ögonblicksbild Cloud App Discovery rapporter](cloud-app-discovery-create-snapshot-reports.md).
3.  Händelser är inte mer än 90 dagar gamla.
4.  Filen är giltig och innehåller information för utgående trafik.

## <a name="supported-firewalls-and-proxy-servers"></a>Stöds brandväggar och proxyservrar

* Barracuda - Brandvägg för webbaserade program (W3C)
* Blue Coat Proxy SG – åtkomstlogg (W3C)
* Check Point
* Cisco ASA FirePOWER
* Cisco ASA-brandväggar (Cisco ASA-brandväggar, ställa in information på 6)
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki – URL-logg
* Clavister nästa generations Brandvägg (Syslog)
* Dell Sonicwall
* Digitala konst i-FILTER
* Fortinets Fortigate
* Juniper SRX
* Juniper SSG
* McAfee säker Webbgateway
* Microsoft Forefront Threat Management Gateway (W3C)
* Palo Alto-brandväggar
* Sophos SG
* Sophos Cyberoam
* SQUID (allmän)
* SQUID (intern)
* Websense – Webbsäkerhetslösningar – undersökande detaljrapport (CSV)
* Websense – Webbsäkerhetslösningar – internetaktivitetslogg (CEF)
* Zscaler

> [!NOTE]
> Cloud App Discovery stöder både IPv4 och IPv6-adresser.

Om din logg inte stöds väljer **andra** som den **datakällan** och anger enheten och logg som du försöker överföra. Loggen granskas av Cloud App Security molnet analytiker team. När stöd för din typ läggs meddelar vi dig, men i stället kan du definiera en anpassad parser som matchar ditt loggformat. Mer information finns i [använder en anpassad logg-parser](https://docs.microsoft.com/cloud-app-security/custom-log-parser).

## <a name="data-attributes-according-to-vendor-documentation"></a>Dataattribut (enligt dokumentationen från leverantören)

| Datakälla         | Riktad App-URL | Den aktuella appen IP-adress | Användarnamn | Ursprungliga IP-adressen | Trafik totalt | Överförda byte |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **Ja**        | **Ja**       | **Ja**  | **Ja**   | Nej            | Nej             |
| Blue Coat                               | **Ja**        | Nej            | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Kontrollpunkt                              | Nej             | **Ja**       | Nej       | **Ja**   | Nej            | Nej             |
| Cisco ASA                               | Nej             | **Ja**       | Nej       | **Ja**   | **Ja**       | Nej             |
| Cisco FWSM                              | Nej             | **Ja**       | Nej       | **Ja**   | **Ja**       | Nej             |
| Cisco Ironport WSA                      | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Cisco Meraki                            | **Ja**        | **Ja**       | Nej       | **Ja**   | Nej            | Nej             |
| Clavister nästa generations Brandvägg (Syslog)                 | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Dell SonicWall                          | **Ja**        | **Ja**       | Nej       | **Ja**   | **Ja**       | **Ja**        |
| FortiGate                               | Nej             | **Ja**       | Nej       | **Ja**   | **Ja**       | **Ja**        |
| Juniper SRX                             | Nej             | **Ja**       | Nej       | **Ja**   | **Ja**       | **Ja**        |
| Juniper SSG                             | Nej             | **Ja**       | Nej       | **Ja**   | **Ja**       | **Ja**        |
| McAfee SWG                              | **Ja**        | Nej            | Nej       | **Ja**   | **Ja**       | **Ja**        |
| MS TMG                                  | **Ja**        | Nej            | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Palo Alto Networks                      | Nej             | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Sophos                                  | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | Nej             |
| SQUID (allmän)                          | **Ja**        | Nej            | **Ja**  | **Ja**   | Nej            | **Ja**        |
| SQUID (intern)                          | **Ja**        | Nej            | **Ja**  | **Ja**   | Nej            | **Ja**        |
| Websense – undersökande rapporten (CSV)   | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Websense – internetaktivitetslogg (CEF)  | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Zscaler                                 | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |


## <a name="next-steps"></a>Nästa steg
Använd följande länkar om du vill fortsätta att ställa in Cloud App Discovery i Azure AD.

* [Skapa ögonblicksrapporter](cloud-app-discovery-create-snapshot-reports.md)
* [Konfigurera kontinuerlig rapportering](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Använda en parser för loggar](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
