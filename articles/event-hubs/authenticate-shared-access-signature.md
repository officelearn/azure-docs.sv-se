---
title: Autentisera åtkomst till Azure Event Hubs med signaturer för delad åtkomst
description: I den här artikeln visas hur du autentiserar åtkomst till eventhubbar-resurser med signaturer för delad åtkomst.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: cde5992355d274410bb43b1e3e60fbba1afe4e44
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676356"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Autentisera åtkomst till eventhubbar-resurser med hjälp av SIGNATURER för delad åtkomst (SAS)
Med SAS (Shared Access Signature) får du detaljerad kontroll över vilken typ av åtkomst du beviljar till de klienter som har signaturen för delad åtkomst. Här är några av de kontroller som du kan ställa in i en SAS: 

- Det intervall som SAS är giltigt över, inklusive starttid och utgångstid.
- De behörigheter som beviljas av SAS. En SAS för ett namnområde för händelsehubbar kan till exempel ge lysningsbehörigheten, men inte sändningsbehörigheten.
- Endast klienter som presenterar giltiga autentiseringsuppgifter kan skicka data till en händelsehubb.
- En klient kan inte utge sig för att vara en annan klient.
- En otillåten klient kan blockeras från att skicka data till en händelsehubb.

Den här artikeln handlar om att autentisera åtkomsten till Event Hubs-resurser med HJÄLP av SAS. Mer information om hur du **auktoriserar** åtkomst till Event Hubs-resurser med SAS finns i [den här artikeln](authorize-access-shared-access-signature.md). 

> [!NOTE]
> Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter när det är möjligt som en säkerhetspraxis, i stället för att använda signaturer för delad åtkomst, som lättare kan äventyras. Även om du kan fortsätta att använda SIGNATURER för delad åtkomst (SAS) för att bevilja finkornig åtkomst till dina Event Hubs-resurser, erbjuder Azure AD liknande funktioner utan att behöva hantera SAS-token eller oroa dig för att återkalla en komprometterad SAS.
> 
> Mer information om Azure AD-integrering i Azure Event Hubs finns i [Auktorisera åtkomst till eventhubbar med Azure AD](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>Konfigurera för SAS-autentisering
Du kan konfigurera eventhubs-behörighetsregeln för delad åtkomst på ett namnområde för eventhubbar eller en entitet (händelsenavinstans eller Kafka-ämne i en händelsehubb). Det går inte att konfigurera en regel för delad åtkomstbehörighet för en konsumentgrupp, men du kan använda regler som konfigurerats på ett namnområde eller entitet för att skydda åtkomsten till konsumentgruppen. 

Följande bild visar hur auktoriseringsreglerna gäller för exempelentiteter. 

![Konfigurera auktoriseringsregel](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

I det här exemplet har exempelnamnet Event Hubs (ExampleNamespace) två entiteter: eh1 och topic1. Auktoriseringsreglerna definieras både på entitetsnivå och även på namnområdesnivå.  

Reglerna för manageRuleNS, sendRuleNS och listenRuleNS gäller både händelsehubbinstansen eh1 och ämne t1. Tillståndsreglerna för listenRule-eh och sendRule-eh gäller endast för händelsenavinstansen eh1 och sendRuleT-auktoriseringsregeln gäller endast för ämnesämne1. 

När du använder sendRuleNS auktoriseringsregel kan klientprogram skicka till både eh1 och topic1. När sendRuleT auktoriseringsregel används, upprätthåller den detaljerad åtkomst till topic1 endast och därmed klientprogram som använder den här regeln för åtkomst nu inte kan skicka till eh1, men bara till topic1.

## <a name="generate-a-shared-access-signature-token"></a>Generera en signaturtoken för delad åtkomst 
Alla klienter som har åtkomst till namnet på ett auktoriseringsregelnamn och en av dess signeringsnycklar kan generera en SAS-token. Token genereras genom att skapa en sträng i följande format:

- `se`– Token utgång omedelbart. Heltalsreflekterande sekunder sedan epok 00:00:00 UTC den 1 januari 1970 (UNIX-epok) när token upphör att gälla
- `skn`– Namn på auktoriseringsregeln, det vill än SAS-nyckelnamn.
- `sr`– URI för den resurs som används.
- `sig`– Signatur.

Signatursträngen är SHA-256-hashen som beräknas över resurs-URI (scope enligt beskrivningen i föregående avsnitt) och strängrepresentationen av tokenutgångstillfället, avgränsad med CRLF.

Hash-beräkningen liknar följande pseudokod och returnerar ett hash-värde på 256 bitar/32 byte. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token innehåller icke-hash-värden så att mottagaren kan återkompensera hash-värdet med samma parametrar och kontrollera att utfärdaren har en giltig signeringsnyckel.

Resurs-URI är den fullständiga URI-resursen för servicebussresursen som åtkomsten begärs till. Till exempel http://<namespace>.servicebus.windows.net/<entityPath> eller `sb://<namespace>.servicebus.windows.net/<entityPath>;` det vill `http://contoso.servicebus.windows.net/eventhubs/eh1`vara, .

URI:n måste vara procentkodad.

Regeln för delad åtkomstauktorisering som används för signering måste konfigureras på den entitet som anges av den här URI:n eller av en av dess hierarkiska överordnade. Till `http://contoso.servicebus.windows.net/eventhubs/eh1` exempel, `http://contoso.servicebus.windows.net` eller i föregående exempel.

En SAS-token är giltig för alla <resourceURI> resurser som föregås av den som används i signatursträngen.

> [!NOTE]
> Du skapar en åtkomsttoken för händelsehubbar med hjälp av principen för delad åtkomst. Mer information finns i [principer för auktorisering för delad åtkomst](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Generera en signatur(token) från en princip 
I följande avsnitt visas generera en SAS-token med hjälp av principer för signatur för delad åtkomst,

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>Java

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Autentisera eventhubbar-utgivare med SAS 
En händelseutgivare definierar en virtuell slutpunkt för en händelsehubb. Utgivaren kan bara användas för att skicka meddelanden till en händelsehubb och inte ta emot meddelanden.

Vanligtvis använder en händelsehubb en utgivare per klient. Alla meddelanden som skickas till någon av utgivarena av en händelsenav är enqueued inom den händelsehubben. Publishers möjliggör finkornig åtkomstkontroll.

Varje Event Hubs-klient tilldelas en unik token som överförs till klienten. Token skapas så att varje unik token ger åtkomst till olika unika utgivare. En klient som innehåller en token kan bara skicka till en utgivare och ingen annan utgivare. Om flera klienter delar samma token delar var och en av dem utgivaren.

Alla token tilldelas med SAS-nycklar. Vanligtvis är alla token signerade med samma nyckel. Klienter är inte medvetna om nyckeln, vilket hindrar klienter från att tillverka token. Klienter fungerar på samma token tills de upphör att gälla.

Om du till exempel vill definiera auktoriseringsregler som inte kan begränsas till att bara skicka/publicera till eventhubbar måste du definiera en regel för skicka auktorisering. Detta kan göras på namnområdesnivå eller ge mer detaljerad omfattning till en viss entitet (händelsehubbarinstans eller ett ämne). En klient eller ett program som är begränsad med sådan detaljerad åtkomst anropas, Event Hubs utgivare. Det gör du på följande sätt:

1. Skapa en SAS-nyckel på den entitet som du vill publicera för att tilldela sändningsomfånget på den. **send** Mer information finns i [principer för auktoriseringsprinciper för delad åtkomst](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Generera en SAS-token med en utgångstid för en viss utgivare med hjälp av nyckeln som genereras i steg1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Ange token till utgivarens klient, som bara kan skicka till entiteten och utgivaren som token ger åtkomst till.

    När token upphör att gälla förlorar klienten sin åtkomst för att skicka/publicera till entiteten. 


> [!NOTE]
> Även om det inte rekommenderas är det möjligt att utrusta enheter med token som ger åtkomst till en händelsehubb eller ett namnområde. Alla enheter som innehåller den här token kan skicka meddelanden direkt till händelsehubben. Dessutom kan enheten inte svartlistas från att skicka till den händelsehubben.
> 
> Det rekommenderas alltid att ge specifika och detaljerade omfattningar.

> [!IMPORTANT]
> När token har skapats etableras varje klient med sin egen unika token.
>
> När klienten skickar data till en händelsehubb taggar den sin begäran med token. För att förhindra att en angripare avlyssnar och stjäl token måste kommunikationen mellan klienten och händelsehubben ske över en krypterad kanal.
> 
> Om en token blir stulen av en angripare kan angriparen personifiera klienten vars token har stulits. Svartlista en utgivare, gör klienten oanvändbar tills den tar emot en ny token som använder en annan utgivare.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Autentisera eventhubbar konsumenter med SAS 
För att autentisera backend-program som använder från data som genereras av Event Hubs-tillverkare kräver Event Hubs tokenautentisering att klienterna antingen har **hanteringsrättigheterna** eller lyssningsbehörigheten tilldelad dess händelsehubbarnamnområde eller händelsenavinstansinstans eller händelsehubbinstans. **listen** Data förbrukas från händelsehubbar med hjälp av konsumentgrupper. Sas-principen ger dig detaljerad omfattning, men det här omfånget definieras endast på entitetsnivå och inte på konsumentnivå. Det innebär att de privilegier som definierats på namnområdesnivå eller händelsenav-instansen eller ämnesnivån tillämpas på konsumentgrupperna för den entiteten.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Auktorisera med SAS](authenticate-shared-access-signature.md)
- [Auktorisera med hjälp av åtkomstkontroll för rollbas (RBAC)](authenticate-shared-access-signature.md)
- [Läs mer om eventhubbar](event-hubs-about.md)

Se följande relaterade artiklar:

- [Autentisera begäranden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
- [Autentisera en hanterad identitet med Azure Active Directory för att komma åt eventhubbarresurser](authenticate-managed-identity.md)
- [Auktorisera åtkomst till eventhubbar-resurser med Azure Active Directory](authorize-access-azure-active-directory.md)
- [Auktorisera åtkomst till eventhubbar-resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)
