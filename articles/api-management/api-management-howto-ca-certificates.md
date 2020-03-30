---
title: Lägga till ett anpassat CERTIFIKATUTfärdarcertifikat – Azure API Management | Microsoft-dokument
description: Lär dig hur du lägger till ett anpassat CA-certifikat i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 21d5869f2bcdfb6383b6ef89869d8098135ea7ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073611"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Så här lägger du till ett anpassat CA-certifikat i Azure API Management

Azure API Management gör det möjligt att installera CERTIFIKATUTFÄRDARcertifikat på datorn i de betrodda rot- och mellanliggande certifikatarkiven. Den här funktionen bör användas om dina tjänster kräver ett anpassat certifikatutfärdarcertifikat.

Artikeln visar hur du hanterar CERTIFIKATUTFÄRDARcertifikat för en Azure API Management-tjänstinstans i Azure-portalen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Ladda upp ett CA-certifikat

![Lägga till certifikatutfärdarcertifikat](media/api-management-howto-ca-certificates/00.png)

Följ stegen nedan för att ladda upp ett nytt certifikatutfärdarcertifikat. Om du inte har skapat en API Management-tjänstinstans ännu läser du självstudien [Skapa en API Management-tjänstinstans](get-started-create-service-instance.md).

1. Navigera till din Azure API Management-tjänstinstans i Azure-portalen.

2. Välj **CERTIFIKATUTFÄRDARcertifikat** på menyn.

3. Klicka på knappen **+ Lägg till**.  

    ![Lägga till certifikatutfärdarcertifikat](media/api-management-howto-ca-certificates/01.png)  

4. Bläddra efter certifikatet och bestäm certifikatarkivet. Endast den offentliga nyckeln behövs, så lösenordet krävs inte.

    ![Lägga till certifikatutfärdarcertifikat](media/api-management-howto-ca-certificates/02.png)  

5. Klicka på **Spara**. Den här åtgärden kan ta några minuter.

    ![Lägga till certifikatutfärdarcertifikat](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Du kan ladda upp `New-AzApiManagementSystemCertificate` ett certifikatutfärdarcertifikat med kommandot Powershell.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Ta bort ett klientcertifikat

Om du vill ta bort ett certifikat klickar du på snabbmenyn **...** och väljer **Ta bort** bredvid certifikatet.

![Ta bort certifikatutfärdare](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
