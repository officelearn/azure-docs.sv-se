---
title: Azure AD Connect transformeringar av moln etablering
description: Den här artikeln beskriver hur du använder omvandlingar för att ändra standardattributen för mappning.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75549303"
---
# <a name="transformations"></a>Transformationer

Med en omvandling kan du ändra standard beteendet för hur ett attribut synkroniseras med Azure Active Directory (Azure AD) med hjälp av moln etablering.

För att kunna utföra den här uppgiften måste du redigera schemat och sedan skicka det igen via en webbegäran.

Mer information om moln etablerings attribut finns i [förstå Azure AD-schemat](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Hämta schemat
Hämta schemat genom att följa stegen i [Visa schemat](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Anpassad attributmappning
Följ dessa steg om du vill lägga till en anpassad attributmappning.

1. Kopiera schemat till en text-eller kod redigerare som [Visual Studio Code](https://code.visualstudio.com/).
1. Leta upp det objekt som du vill uppdatera i schemat.

   ![Objekt i schemat](media/how-to-transformation/transform1.png)</br>
1. Leta upp koden för `ExtensionAttribute3` objektet användare.

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
1. Redigera koden så att attributet Company mappas till `ExtensionAttribute3`.

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
 1. Kopiera schemat tillbaka till Graph Explorer, ändra typ av **begäran** till **Lägg**och välj **Kör fråga**.

    ![Köra fråga](media/how-to-transformation/transform2.png)

 1. Gå nu till konfigurations konfigurationen för molnet i Azure Portal och välj **starta om etablering**.

    ![Starta om etablering](media/how-to-transformation/transform3.png)

 1. Efter en liten stund kontrollerar du att attributen fylls genom att köra följande fråga i Graph Explorer: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`.
 1. Du bör nu se värdet.

    ![Värdet visas](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mappning av anpassade attribut med funktion
För mer avancerade mappningar kan du använda funktioner som gör att du kan manipulera data och skapa värden för attribut som passar organisationens behov.

Följ föregående steg och redigera sedan den funktion som används för att konstruera det slutliga värdet för att utföra den här uppgiften.

Information om syntax och exempel på uttryck finns i [skriva uttryck för mappningar av attribut i Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
