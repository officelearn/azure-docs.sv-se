---
title: Azure AD Connect transformeringar av moln etablering
description: Det här dokumentet beskriver hur du använder omvandlingar för att ändra standardattributen för mappning.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794476"
---
# <a name="transformations"></a>Transformationer

Med en omvandling kan du ändra standard beteendet för hur ett attribut synkroniseras med Azure AD med hjälp av moln etablering.  

För att kunna utföra den här uppgiften måste du redigera schemat och sedan skicka det igen via en webbegäran.

Mer information om moln etablerings attribut finns i [förstå Azure AD-schemat](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Hämta schemat
Hämta schemat med hjälp av stegen som beskrivs i [Visa schemat](concept-attributes.md#viewing-the-schema). 


## <a name="custom-attribute-mapping"></a>Anpassad attributmappning
Använd följande procedur om du vill lägga till en anpassad attributmappning.

1. Kopiera schemat till en text-eller kod redigerare som [Visual Studio Code](https://code.visualstudio.com/).  
2. Leta upp det objekt som du vill uppdatera i schemat ![](media/how-to-transformation/transform1.png)</br>
3. Leta upp koden för **ExtensionAttribute3** under objektet User.

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
 4.  Redigera koden så att attributet Company mappas till ExtensionAttribute3.
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
 5. Kopiera schemat tillbaka till Graph Explorer, ändra typ av begäran till att skicka och **köra fråga**.  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  Gå nu till konfigurations konfigurationen för molnet i Azure Portal och **starta om etableringen**.
 ![](media/how-to-transformation/transform3.png)</br>
 7.  Efter en liten stund kontrollerar du att attributen fylls genom att köra följande fråga i Graph Explorer: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`.
 8.  Du bör nu se värdet.
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>Mappning av anpassade attribut med funktion
För mer avancerade mappningar kan du använda funktioner som gör att du kan manipulera data och skapa värden för attribut som passar dina organisationers behov.

För att utföra den här uppgiften följer du bara stegen ovan och redigerar sedan den funktion som används för att skapa det slutliga värdet.

Information om syntaxen och exempel på uttryck finns i [skriva uttryck för mappning av attribut i Azure Active Directory](reference-expressions.md)


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud revisioner?](what-is-cloud-provisioning.md)
