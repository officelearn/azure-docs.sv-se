---
title: Omvandlingar för azure AD Connect-molnetablering
description: I den här artikeln beskrivs hur du använder omvandlingar för att ändra standardattributmappningarna.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549303"
---
# <a name="transformations"></a>Transformationer

Med en omvandling kan du ändra standardbeteendet för hur ett attribut synkroniseras med Azure Active Directory (Azure AD) med hjälp av molnetablering.

För att kunna utföra den här uppgiften måste du redigera schemat och sedan skicka det igen via en webbbegäran.

Mer information om molnetableringsattribut finns [i Förstå Azure AD-schemat](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Hämta schemat
Om du vill hämta schemat följer du stegen i [Visa schemat](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Anpassad attributmappning
Så här lägger du till en anpassad attributmappning.

1. Kopiera schemat till en text- eller kodredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/).
1. Leta reda på det objekt som du vill uppdatera i schemat.

   ![Objekt i schemat](media/how-to-transformation/transform1.png)</br>
1. Leta reda `ExtensionAttribute3` på koden för under användarobjektet.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Redigera koden så att företagsattributet `ExtensionAttribute3`mappas till .

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Kopiera schemat tillbaka till Graph Explorer, ändra **typen Begäran** till **PUT**och välj **Kör fråga**.

    ![Köra fråga](media/how-to-transformation/transform2.png)

 1. Nu, i Azure-portalen, gå till molnet etablering konfiguration och välj **Starta etablering**.

    ![Starta om etablering](media/how-to-transformation/transform3.png)

 1. Efter en liten stund kontrollerar du att attributen fylls i `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`genom att köra följande fråga i Graph Explorer: .
 1. Du bör nu se värdet.

    ![Värdet visas](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Anpassad attributmappning med funktion
För mer avancerad mappning kan du använda funktioner som gör att du kan ändra data och skapa värden för attribut som passar organisationens behov.

För att göra den här uppgiften följer du föregående steg och redigerar sedan funktionen som används för att konstruera det slutliga värdet.

Information om syntaxen och exempel på uttryck finns i [Skriva uttryck för attributmappningar i Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
