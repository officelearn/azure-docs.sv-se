---
title: Apache Kafka REST-proxy - Azure HDInsight
description: Lär dig hur du utför Apache Kafka-åtgärder med hjälp av en Kafka REST-proxy på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758994"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagera med Apache Kafka-kluster i Azure HDInsight med hjälp av en REST-proxy

Kafka REST Proxy kan du interagera med din Kafka kluster via en REST API över HTTP. Den här åtgärden innebär att dina Kafka-klienter kan vara utanför ditt virtuella nätverk. Klienter kan ringa enkla HTTP-anrop till Kafka-klustret i stället för att förlita sig på Kafka-bibliotek. Den här artikeln visar hur du skapar en REST-proxy aktiverad Kafka-kluster. Innehåller också en exempelkod som visar hur du ringer anrop till REST-proxy.

## <a name="rest-api-reference"></a>Referens för REST-API

För åtgärder som stöds av Kafka REST API, se [HDInsight Kafka REST Proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Bakgrund

![Kafka REST proxy design](./media/rest-proxy/rest-proxy-architecture.png)

Den fullständiga specifikationen av åtgärder som stöds av API:et finns i [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>SLUTPUNKT FÖR REST-proxy

När du skapar ett HDInsight Kafka-kluster med REST-proxy skapas en ny offentlig **Properties** slutpunkt för klustret, som du hittar i dina HDInsight-klusteregenskaper på Azure-portalen.

### <a name="security"></a>Säkerhet

Åtkomst till Kafka REST-proxyn hanteras med Azure Active Directory-säkerhetsgrupper. När du skapar Kafka-klustret ska du tillhandahålla säkerhetsgruppen Azure AD med REST-slutpunktsåtkomst. Kafka-klienter som behöver åtkomst till REST-proxyn ska registreras i den här gruppen av gruppägaren. Gruppägaren kan registrera sig via Portalen eller via PowerShell.

För REST-proxyslutpunktsbegäranden bör klientprogram hämta en OAuth-token. Token används för att verifiera medlemskap i säkerhetsgruppen. Hitta ett [exempel på klientprogram](#client-application-sample) nedan som visar hur du skaffar en OAuth-token. Klientprogrammet skickar OAuth-token i HTTP-begäran till REST-proxyn.

> [!NOTE]  
> Mer information om AAD-säkerhetsgrupper finns i [Hantera app- och resursåtkomst med Hjälp av Azure Active Directory-grupper.](../../active-directory/fundamentals/active-directory-manage-groups.md) Mer information om hur OAuth-token fungerar finns i [Auktorisera åtkomst till Azure Active Directory-webbprogram med hjälp av kodbidragsflödet för OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Krav

1. Registrera ett program med Azure AD. Klientprogrammen som du skriver för att interagera med Kafka REST-proxyn använder programmets ID och hemlighet för att autentisera till Azure.

1. Skapa en Azure AD-säkerhetsgrupp. Lägg till programmet som du har registrerat med Azure AD i säkerhetsgruppen som **medlem** i gruppen. Den här säkerhetsgruppen används för att styra vilka program som tillåts interagera med REST-proxyn. Mer information om hur du skapar Azure AD-grupper finns i [Skapa en grundläggande grupp och lägga till medlemmar med Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Verifiera gruppen är av typen **Säkerhet**.
    ![Säkerhetsgrupp](./media/rest-proxy/rest-proxy-group.png)

    Verifiera att programmet är medlem i Gruppen.
    ![Kontrollera medlemskap](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Skapa ett Kafka-kluster med REST-proxy aktiverad

1. Kontrollera alternativet **Aktivera Kafka REST-proxy** under arbetsflödet för att skapa Kafka-kluster. **Security + networking**

     ![Aktivera Kafka REST-proxy och välj säkerhetsgrupp](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klicka på **Välj säkerhetsgrupp**. Välj den säkerhetsgrupp som du vill ha åtkomst till REST-proxyn i listan över säkerhetsgrupper. Du kan använda sökrutan för att hitta rätt säkerhetsgrupp. Klicka på knappen **Välj** längst ned.

     ![Aktivera Kafka REST-proxy och välj säkerhetsgrupp](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Slutför de återstående stegen för att skapa klustret enligt beskrivningen i [Skapa Apache Kafka-kluster i Azure HDInsight med Azure-portalen](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. När klustret har skapats går du till klusteregenskaperna för att registrera Kafka REST-proxy-URL:en.

     ![visa REST-proxy-URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Exempel på klientprogram

Du kan använda pythonkoden nedan för att interagera med REST-proxyn i Kafka-klustret. Så här använder du kodexemplet:

1. Spara exempelkoden på en dator med Python installerat.
1. Installera nödvändiga python-beroenden `pip3 install msal`genom att köra .
1. Ändra kodavsnittet **Konfigurera dessa egenskaper** och uppdatera följande egenskaper för din miljö:

    |Egenskap |Beskrivning |
    |---|---|
    |Klient-ID:t|Azure-klienten där din prenumeration finns.|
    |Klientorganisations-ID|ID:t för programmet som du registrerade i säkerhetsgruppen.|
    |Klienthemlighet|Hemligheten för programmet som du registrerade i säkerhetsgruppen.|
    |Kafkarest_endpoint|Hämta det här värdet från fliken **Egenskaper** i klusteröversikten enligt beskrivningen i [distributionsavsnittet](#create-a-kafka-cluster-with-rest-proxy-enabled). Det bör vara i följande format -`https://<clustername>-kafkarest.azurehdinsight.net`|

1. Kör python-filen från kommandoraden genom att köra python-filen`sudo python3 <filename.py>`

Den här koden gör följande åtgärd:

1. Hämtar en OAuth-token från Azure AD.
1. Visar hur du gör en begäran till Kafka REST proxy.

Mer information om hur du hämtar OAuth-token i python finns i [klassen Python AuthenticationContext](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Du kan se `topics` en fördröjning medan som inte skapas eller tas bort via Kafka REST proxy återspeglas där. Den här fördröjningen beror på cacheuppdatering.

```python
#Required python packages
#pip3 install msal

import msal

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Nedan ett annat exempel på hur du hämtar en token från Azure för REST-proxy med hjälp av ett curl-kommando. **Observera att vi `scope=https://hib.azurehdinsight.net/.default` behöver den angivna samtidigt som du får en token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Nästa steg

* [Kafka REST proxy API referensdokument](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
