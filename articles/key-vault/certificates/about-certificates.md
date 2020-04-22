---
title: Om Azure Key Vault-certifikat – Azure Key Vault
description: Översikt över AZURE Key Vault REST-gränssnitt och certifikat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 5e014634ecb251f05710de16daee30d72dae619e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685903"
---
# <a name="about-azure-key-vault-certificates"></a>Om Azure Key Vault-certifikat

Support för Key Vault-certifikat ger hantering av dina x509-certifikat och följande beteenden:  

-   Gör att en certifikatägare kan skapa ett certifikat genom en skapandeprocess för nyckelvalv eller genom import av ett befintligt certifikat. Innehåller både självsignerade certifikat och certifikatutfärdare genererade certifikat.
-   Gör att en Key Vault-certifikatägare kan implementera säker lagring och hantering av X509-certifikat utan interaktion med privat nyckelmaterial.  
-   Gör att en certifikatägare kan skapa en princip som styr Key Vault för att hantera livscykeln för ett certifikat.  
-   Gör det möjligt för certifikatägare att tillhandahålla kontaktinformation för meddelande om livscykelhändelser för utgångsdatum och förnyelse av certifikat.  
-   Stöder automatisk förnyelse med utvalda utfärdare - Key Vault-partner X509 certifikatleverantörer / certifikatutfärdare.

>[!Note]
>Icke-partnerleverantörer/myndigheter är också tillåtna, men kommer inte att stödja funktionen för automatisk förnyelse.

## <a name="composition-of-a-certificate"></a>Ett certifikats sammansättning

När ett Key Vault-certifikat skapas skapas också en adresserbar nyckel och hemlighet med samma namn. Key Vault-nyckeln tillåter nyckelåtgärder och Key Vault-hemligheten gör det möjligt att hämta certifikatvärdet som en hemlighet. Ett Key Vault-certifikat innehåller också offentliga x509-certifikatmetadata.  

Identifieraren och versionen av certifikat liknar den för nycklar och hemligheter. En specifik version av en adresserbar nyckel och hemlighet som skapats med certifikatversionen av Key Vault finns i certifikatsvaret För Nyckelvalv.
 
![Certifikat är komplexa objekt](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Exporteras eller icke-exportbar nyckel

När ett Key Vault-certifikat skapas kan det hämtas från den adresserbara hemligheten med den privata nyckeln i antingen PFX- eller PEM-format. Principen som används för att skapa certifikatet måste ange att nyckeln kan exporteras. Om principen anger att den inte kan exporteras är den privata nyckeln inte en del av värdet när den hämtas som en hemlighet.  

Den adresserbara nyckeln blir mer relevant med KV-certifikat som inte kan exporteras. Den adresserbara KV-nyckelns åtgärder mappas från *nyckelanvändarfält* i KV-certifikatprincipen som används för att skapa KV-certifikatet.  

Två typer av nyckel stöds – *RSA* eller *RSA HSM* med certifikat. Exportbar är endast tillåten med RSA, som inte stöds av RSA HSM.  

## <a name="certificate-attributes-and-tags"></a>Certifikatattribut och -taggar

Förutom certifikatmetadata, en adresserbar nyckel och adresserbar hemlighet innehåller ett Key Vault-certifikat också attribut och taggar.  

### <a name="attributes"></a>Attribut

Certifikatattributen speglas till attribut för den adresserbara nyckeln och hemlighet som skapas när KV-certifikat skapas.  

Ett Key Vault-certifikat har följande attribut:  

-   *aktiverad:* booleska, valfria, standard är **sant**. Kan anges för att ange om certifikatdata kan hämtas som hemliga eller fungerande som en nyckel. Används även tillsammans med *nbf* och *exp* när en operation inträffar mellan *nbf* och *exp*, och kommer endast att tillåtas om aktiverad är inställd på true. Åtgärder utanför *nbf-* och *exp-fönstret* tillåts automatiskt.  

Det finns ytterligare skrivskyddade attribut som ingår i svaret:

-   *skapad*: IntDate: anger när den här versionen av certifikatet skapades.  
-   *uppdaterad*: IntDate: anger när den här versionen av certifikatet uppdaterades.  
-   *exp*: IntDate: innehåller värdet för utgångsdatumet för x509-certifikatet.  
-   *nbf*: IntDate: innehåller värdet för datumet för x509-certifikatet.  

> [!Note] 
> Om ett Key Vault-certifikat upphör att gälla är den adresserbar nyckel och hemligheten blir obrukbar.  

### <a name="tags"></a>Taggar

 Klientens angivna ordlista med nyckelvärdespar, liknande taggar i nycklar och hemligheter.  

 > [!Note]
> Taggar kan läsas av en uppringare om de har *listan* eller *får* behörighet till objekttypen (nycklar, hemligheter eller certifikat).

## <a name="certificate-policy"></a>Certifikatprincip

En certifikatprincip innehåller information om hur du skapar och hanterar livscykeln för ett Key Vault-certifikat. När ett certifikat med privat nyckel importeras till nyckelvalvet skapas en standardprincip genom att läsa x509-certifikatet.  

När ett Key Vault-certifikat skapas från grunden måste en princip anges. Principen anger hur du skapar den här nyckelvalvscertifikatversionen eller nästa nyckelvalvscertifikatversion. När en princip har upprättats krävs det inte med successiva skapa åtgärder för framtida versioner. Det finns bara en instans av en princip för alla versioner av ett Key Vault-certifikat.  

På en hög nivå innehåller en certifikatprincip följande information:  

-   X509-certifikategenskaper: Innehåller ämnesnamn, ämnes alternativa namn och andra egenskaper som används för att skapa en x509-certifikatbegäran.  
-   Nyckelegenskaper: innehåller nyckeltyp, nyckellängd, exportbara och återanvändning av nyckelfält. Dessa fält instruera nyckelvalv om hur du genererar en nyckel.  
-   Hemliga egenskaper: innehåller hemliga egenskaper som innehållstyp av adresserbar hemlighet för att generera det hemliga värdet, för att hämta certifikat som en hemlighet.  
-   Livstidsåtgärder: innehåller livstidsåtgärder för KV-certifikatet. Varje livstidsåtgärd innehåller:  

     - Utlösare: angiven via dagar före utgången eller livstidsintervallprocent  

     - Åtgärd: ange åtgärdstyp – *e-postKontakt eller* *återskapa automatiskt*  

-   Utfärdare: Parametrar om certifikatutfärdaren som ska användas för att utfärda x509-certifikat.  
-   Principattribut: innehåller attribut som är associerade med principen  

### <a name="x509-to-key-vault-usage-mapping"></a>X509 till användningsmappning för Nyckelvalv

Följande tabell representerar mappningen av x509-nyckelanvändningsprincipen till effektiva nyckelåtgärder för en nyckel som skapats som en del av skapandet av nyckelvalvscertifikat.

|**Flaggor för X509-nyckelanvändning**|**Nyckelvalvs-nyckel ops**|**Standardbeteende**|
|----------|--------|--------|
|DataEncipherment|kryptera, dekryptera| Ej tillämpligt |
|DechiffreraOnly|Dekryptera| Ej tillämpligt  |
|DigitalSignature|tecken, verifiera| Standard för Key Vault utan användningsspecifikation vid skapande av certifikat | 
|EncipherOnly|encrypt| Ej tillämpligt |
|KeyCertSign|tecken, verifiera|Ej tillämpligt|
|KeyEncipherment|wrapKey, ta bort tangenter| Standard för Key Vault utan användningsspecifikation vid skapande av certifikat | 
|Oavvislighet|tecken, verifiera| Ej tillämpligt |
|crlsign (crlsign)|tecken, verifiera| Ej tillämpligt |

## <a name="certificate-issuer"></a>Certifikatutfärdare

Ett Key Vault-certifikatobjekt innehåller en konfiguration som används för att kommunicera med en vald certifikatutfärdare för att beställa x509-certifikat.  

-   Key Vault samarbetar med följande certifikatutfärdare för TLS/SSL-certifikat

|**Leverantörens namn**|**Platser**|
|----------|--------|
|DigiCert|Stöds på alla viktiga valvtjänstplatser i offentliga moln och Azure Government|
|GlobalSign|Stöds på alla viktiga valvtjänstplatser i offentliga moln och Azure Government|

Innan en certifikatutfärdare kan skapas i ett nyckelvalv måste följande nödvändiga steg 1 och 2 utföras.  

1. Ombord på certifikatutfärdare (CA)-leverantörer  

    -   En organisationsadministratör måste vara ombord på sitt företag (t.ex. Contoso) med minst en CA-leverantör.  

2. Admin skapar autentiseringsuppgifter för beställare för Key Vault för att registrera (och förnya) TLS/SSL-certifikat  

    -   Tillhandahåller den konfiguration som ska användas för att skapa ett utfärdarobjekt för providern i nyckelvalvet  

Mer information om hur du skapar Utfärdarobjekt från certifikatportalen finns i [bloggen Nyckelvalvscertifikat](https://aka.ms/kvcertsblog)  

Key Vault gör det möjligt att skapa flera utfärdarobjekt med olika konfiguration av utfärdare provider. När ett utfärdarobjekt har skapats kan dess namn refereras i en eller flera certifikatprinciper. Om du refererar till utfärdarobjektet instrueras Key Vault att använda konfigurationen enligt vad som anges i utfärdarobjektet när x509-certifikatet från CERTIFIKATUTfärdaren begärs från certifikatutfärdaren när certifikatet skapas och förnyas.  

Utfärdarobjekt skapas i valvet och kan endast användas med KV-certifikat i samma valv.  

## <a name="certificate-contacts"></a>Certifikatkontakter

Certifikatkontakter innehåller kontaktinformation för att skicka meddelanden som utlöses av certifikatets livstidshändelser. Kontaktinformationen delas av alla certifikat i nyckelvalvet. Ett meddelande skickas till alla angivna kontakter för en händelse för alla certifikat i nyckelvalvet.  

Om ett certifikats princip är inställd på automatisk förnyelse skickas ett meddelande om följande händelser.  

- Före förnyelse av certifikat
- Efter förnyelse av certifikatet, med angivande av om certifikatet har förnyats, eller om det uppstod ett fel, vilket kräver manuell förnyelse av certifikatet.  

  När en certifikatprincip som har angetts för att förnyas manuellt (endast e-post) skickas ett meddelande när det är dags att förnya certifikatet.  

## <a name="certificate-access-control"></a>Kontroll av certifikatåtkomst

 Åtkomstkontrollen för certifikat hanteras av Key Vault och tillhandahålls av Nyckelvalvet som innehåller dessa certifikat. Åtkomstkontrollprincipen för certifikat skiljer sig från åtkomstkontrollprinciperna för nycklar och hemligheter i samma Nyckelvalv. Användare kan skapa ett eller flera valv för att lagra certifikat, för att upprätthålla scenariot lämplig segmentering och hantering av certifikat.  

 Följande behörigheter kan användas per huvudmansbasis i åtkomstkontrollposten hemligheter i ett nyckelvalv och speglar noggrant de åtgärder som tillåts för ett hemligt objekt:  

- Behörigheter för certifikathanteringsåtgärder
  - *hämta:* Hämta den aktuella certifikatversionen eller någon version av ett certifikat 
  - *lista*: Lista aktuella certifikat eller versioner av ett certifikat  
  - *uppdatering*: Uppdatera ett certifikat
  - *skapa*: Skapa ett Key Vault-certifikat
  - *importera*: Importera certifikatmaterial till ett Key Vault-certifikat
  - *ta bort*: Ta bort ett certifikat, dess princip och alla dess versioner  
  - *återställa*: Återställa ett borttaget certifikat
  - *säkerhetskopiering*: Säkerhetskopiera ett certifikat i ett nyckelvalv
  - *återställning:* Återställa ett säkerhetskopierat certifikat till ett nyckelvalv
  - *hanterakontakter*: Hantera key vault-certifikatkontakter  
  - *manageissuers*: Hantera certifikatutfärdare/utfärdare av key vault-certifikat
  - *getissuers*: Få ett certifikat myndigheter / emittenter
  - *listissuers*: Lista ett certifikat myndigheter/emittenter  
  - *setissuers*: Skapa eller uppdatera ett key vault-certifikats myndigheter/utfärdare  
  - *deleteissuers*: Ta bort en Key Vault-certifikat myndigheter / emittenter  
 
- Behörigheter för privilegierade åtgärder
  - *rensa*: Rensa (permanent ta bort) ett borttaget certifikat

Mer information finns [i certifikatåtgärderna i REST-API-referensen för Nyckelvalvet](/rest/api/keyvault). Information om hur du upprättar behörigheter finns i [Arkiv - Skapa eller Uppdatera](/rest/api/keyvault/vaults/createorupdate) och [Valv - Uppdatera åtkomstprincip](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Nästa steg

- [Om Key Vault](../general/overview.md)
- [Om nycklar, hemligheter och certifikat](../general/about-keys-secrets-certificates.md)
- [Om nycklar](../keys/about-keys.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)