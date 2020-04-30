---
title: Apache Kafka REST-proxy – Azure HDInsight
description: Lär dig hur du gör Apache Kafka åtgärder med en Kafka REST-proxy på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758994"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagera med Apache Kafka kluster i Azure HDInsight med hjälp av en REST-proxy

Med Kafka REST proxy kan du interagera med ditt Kafka-kluster via en REST API över HTTP. Den här åtgärden innebär att dina Kafka-klienter kan vara utanför det virtuella nätverket. Klienter kan göra enkla HTTP-anrop till Kafka-klustret i stället för att förlita dig på Kafka-bibliotek. I den här artikeln visas hur du skapar ett Kafka-kluster med REST proxy. Innehåller också en exempel kod som visar hur du gör anrop till REST-proxy.

## <a name="rest-api-reference"></a>Referens för REST-API

För åtgärder som stöds av Kafka-REST API, se [HDInsight KAFKA rest proxy API-referens](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Bakgrund

![Kafka REST-proxy design](./media/rest-proxy/rest-proxy-architecture.png)

En fullständig specifikation av de åtgärder som stöds av API: et finns [Apache KAFKA rest proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>REST proxy-slutpunkt

När du skapar ett HDInsight Kafka-kluster med REST proxy skapas en ny offentlig slut punkt för klustret, som du kan hitta i HDInsight-klustrets **Egenskaper** på Azure Portal.

### <a name="security"></a>Säkerhet

Åtkomst till Kafka REST proxy hanteras med Azure Active Directory säkerhets grupper. När du skapar Kafka-klustret anger du Azure AD-säkerhetsgruppen med REST Endpoint Access. Kafka-klienter som behöver åtkomst till REST-proxyn ska registreras till den här gruppen av grupp ägaren. Grupp ägaren kan registreras via portalen eller via PowerShell.

Klient program bör hämta en OAuth-token för REST-slut punkts begär Anden. Token används för att verifiera medlemskap i säkerhets grupper. Hitta ett [exempel på klient program](#client-application-sample) nedan som visar hur du skaffar en OAuth-token. Klient programmet skickar OAuth-token i HTTP-begäran till REST-proxyn.

> [!NOTE]  
> Mer information om AAD-säkerhetsgrupper finns i [Hantera app-och resurs åtkomst med hjälp av Azure Active Directory grupper](../../active-directory/fundamentals/active-directory-manage-groups.md). Mer information om hur OAuth-token fungerar finns i [ge åtkomst till Azure Active Directory webb program med hjälp av OAuth 2,0 kod tilldelnings flödet](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Krav

1. Registrera ett program med Azure AD. Klient programmen som du skriver för att interagera med Kafka REST-proxyn använder programmets ID och hemlighet för att autentisera till Azure.

1. Skapa en Azure AD-säkerhetsgrupp. Lägg till det program som du har registrerat med Azure AD i säkerhets gruppen som **medlem** i gruppen. Den här säkerhets gruppen används för att kontrol lera vilka program som får interagera med REST-proxyn. Mer information om hur du skapar Azure AD-grupper finns i [skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Verifiera att gruppen är av typen **säkerhet**.
    ![Säkerhets grupp](./media/rest-proxy/rest-proxy-group.png)

    Verifiera att programmet är medlem i gruppen.
    ![Kontrol lera medlemskap](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Skapa ett Kafka-kluster med REST proxy aktiverat

1. Under arbets flödet för skapande av Kafka-kluster går du till fliken **säkerhet + nätverk** och markerar alternativet **Aktivera Kafka rest proxy** .

     ![Aktivera Kafka REST proxy och välj säkerhets grupp](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klicka på **Välj säkerhets grupp**. I listan över säkerhets grupper väljer du den säkerhets grupp som du vill ha åtkomst till REST-proxyn. Du kan använda sökrutan för att hitta rätt säkerhets grupp. Klicka på knappen **Välj** längst ned.

     ![Aktivera Kafka REST proxy och välj säkerhets grupp](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Slutför de återstående stegen för att skapa klustret enligt beskrivningen i [skapa Apache Kafka kluster i Azure HDInsight med Azure Portal](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. När klustret har skapats går du till kluster egenskaperna för att registrera Kafka REST proxy-URL: en.

     ![Visa URL för REST-proxy](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Exempel på klient program

Du kan använda python-koden nedan för att interagera med REST-proxyn på ditt Kafka-kluster. Följ dessa steg om du vill använda kod exemplet:

1. Spara exempel koden på en dator med python installerat.
1. Installera nödvändiga python-beroenden genom `pip3 install msal`att köra.
1. Ändra kod avsnittet **Konfigurera dessa egenskaper** och uppdatera följande egenskaper för din miljö:

    |Egenskap |Beskrivning |
    |---|---|
    |Klientorganisations-ID|Azure-klienten där din prenumeration är.|
    |Klientorganisations-ID|ID för programmet som du registrerade i säkerhets gruppen.|
    |Klienthemlighet|Hemligheten för det program som du registrerade i säkerhets gruppen.|
    |Kafkarest_endpoint|Hämta det här värdet från fliken **Egenskaper** i kluster översikten enligt beskrivningen i [avsnittet distribution](#create-a-kafka-cluster-with-rest-proxy-enabled). Det ska ha följande format –`https://<clustername>-kafkarest.azurehdinsight.net`|

1. Kör python-filen från kommando raden genom att köra`sudo python3 <filename.py>`

Den här koden utför följande åtgärd:

1. Hämtar en OAuth-token från Azure AD.
1. Visar hur du gör en begäran till Kafka REST proxy.

Mer information om hur du hämtar OAuth-tokens i python finns i [python AuthenticationContext-klass](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Du kan se en fördröjning när `topics` den inte skapas eller tas bort via Kafka rest-proxyn visas där. Den här fördröjningen beror på cache-uppdateringen.

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

Det finns ett annat exempel på hur du hämtar en token från Azure för REST-proxy med hjälp av ett spiral kommando. **Observera att vi behöver det `scope=https://hib.azurehdinsight.net/.default` angivna när du hämtar en token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Nästa steg

* [Kafka REST proxy API-referens dokument](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
