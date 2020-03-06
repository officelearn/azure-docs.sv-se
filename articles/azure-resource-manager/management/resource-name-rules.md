---
title: Begränsningar för resurs namn
description: Visar regler och begränsningar för namngivning av Azure-resurser.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 7b6a9e16ba28a2a0e5e4e181dc5650d2110eab88
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391176"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Namngivningsregler och begränsningar för Azure-resurser

Den här artikeln sammanfattar namngivnings regler och begränsningar för Azure-resurser. Rekommendationer om hur du namnger resurser finns i [Ready: rekommenderade namngivnings-och taggnings konventioner](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

Resurs namn är inte Skift läges känsliga om de inte uttryckligen anges i kolumnen giltiga tecken.

I följande tabeller refererar termen alfanumeriskt till:

* **a** till **z** (små bokstäver)
* **A** till **Z** (versaler)
* **0** till **9** (siffror)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | brygghuvudservrar | Resursgrupp | 3–63 | Små bokstäver och siffror.<br><br>Börja med gemener. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | tjänst | EAN | 1-50 | Alfanumeriska tecken.<br><br>Börja med en bokstav. |
> | tjänst/API: er | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | Service/API/problem | api | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/API/problem/bilagor | ge | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | Service/API/problem/kommentarer | ge | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/API/åtgärder | api | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/API: er/åtgärder/Taggar | operation | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/API: er/versioner | api | 1–80 | Alfanumeriska tecken, under streck och bindestreck.<br><br>Börja och sluta med alfanumeriskt eller under streck. |
> | tjänst/API/scheman | api | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | Service/API: er/tagDescriptions | api | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/API/Taggar | api | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | Service/API-version – uppsättningar | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst-authorizationServers | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/Server delar | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/certifikat | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/diagnostik | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/grupper | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/grupper/användare | grupp | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst-Identityprovider | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/loggar | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/meddelanden | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/meddelanden/recipientEmails | avisering | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst-openidConnectProviders | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/principer | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/produkter | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | service/produkter/API: er | produkt | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/produkter/grupper | produkt | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | service/produkter/Taggar | produkt | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/egenskaper | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/prenumerationer | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | service/Taggar | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/mallar | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/användare | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | configurationStores | Resursgrupp | 5-50 | Alfanumeriska tecken, under streck och bindestreck. |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | hålls | tilldelnings omfång | 1–90 | Alfanumeriska tecken, punkter, under streck, bindestreck och parenteser.<br><br>Kan inte sluta med punkt. |
> | policyassignments | tilldelnings omfång | 1-128 visnings namn<br><br>1-260 resurs namn | Visnings namnet får innehålla tecken.<br><br>Resurs namnet får inte innehålla `%` och får inte sluta med punkt eller blank steg. |
> | policydefinitions | definitions område | 1-128 visnings namn<br><br>1-260 resurs namn | Visnings namnet får innehålla tecken.<br><br>Resurs namnet får inte innehålla `%` och får inte sluta med punkt eller blank steg. |
> | policySetDefinitions | definitions område | 1-128 visnings namn<br><br>1-260 resurs namn | Visnings namnet får innehålla tecken.<br><br>Resurs namnet får inte innehålla `%` och får inte sluta med punkt eller blank steg.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | automationAccounts | Resursgrupp | 6-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med bokstav och sluta med alfanumeriska tecken. |
> | automationAccounts/certifikat | Automation-konto | 1-128 | Kan inte använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med blank steg.  |
> | automationAccounts/anslutningar | Automation-konto | 1-128 | Kan inte använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med blank steg. |
> | automationAccounts/autentiseringsuppgifter | Automation-konto | 1-128 | Kan inte använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med blank steg. |
> | automationAccounts/Runbooks | Automation-konto | 1–63 | Alfanumeriska tecken, under streck och bindestreck.<br><br>Börja med en bokstav.  |
> | automationAccounts/scheman | Automation-konto | 1-128 | Kan inte använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med blank steg. |
> | automationAccounts/variabler | Automation-konto | 1-128 | Kan inte använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med blank steg. |
> | automationAccounts/Watcher | Automation-konto | 1–63 |  Alfanumeriska tecken, under streck och bindestreck.<br><br>Börja med en bokstav. |
> | automationAccounts/Webhooks | Automation-konto | 1-128 | Kan inte använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med blank steg. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | batchAccounts | Region | 3–24 | Små bokstäver och siffror. |
> | batchAccounts/program | Batch-konto | 1-64 | Alfanumeriska tecken, under streck och bindestreck. |
> | batchAccounts/certifikat | Batch-konto | 5-45 | Alfanumeriska tecken, under streck och bindestreck. |
> | batchAccounts/pooler | Batch-konto | 1-64 | Alfanumeriska tecken, under streck och bindestreck. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | blockchainMembers | EAN | 2-20 | Små bokstäver och siffror.<br><br>Börja med gemener. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | botServices | EAN | 2–64 |  Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |
> | botServices/kanaler | bot-tjänst | 2–64 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |
> | botServices/anslutningar | bot-tjänst | 2–64 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |
> | enterpriseChannels | Resursgrupp | 2–64 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Redis | EAN | 1–63 | Alfanumeriska tecken och bindestreck.<br><br>Börja och sluta med alfanumeriska tecken. Efterföljande bindestreck är inte tillåtna. |
> | Redis/firewallRules | Redis | 1-256 | Alfanumeriska tecken |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | profiles | Resursgrupp | 1-260 | Alfanumeriska tecken och bindestreck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | profiler/slut punkter | EAN | 1-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja och sluta med alfanumeriska tecken. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | certificateOrders | Resursgrupp | 3-30 | Alfanumeriska tecken. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | Resursgrupp | 2–64 | Alfanumeriska tecken och bindestreck.<br><br>Börja och sluta med alfanumeriska tecken. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | availabilitySets | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slutar med alfanumeriskt eller under streck. |
> | diskEncryptionSets | Resursgrupp | 1–80 | Alfanumeriska tecken och under streck. |
> | disk | Resursgrupp | 1–80 | Alfanumeriska tecken och under streck. |
> | gallerier | Resursgrupp | 1–80 | Alfanumeriska tecken och punkter.<br><br>Börja och sluta med alfanumeriska tecken. |
> | gallerier/program | formatmall | 1–80 | Alfanumeriska tecken, bindestreck och punkter.<br><br>Börja och sluta med alfanumeriska tecken. |
> | gallerier/program/versioner | applicering | 32-bitars heltal | Siffror och punkter. |
> | gallerier/bilder | formatmall | 1–80 | Alfanumeriska tecken, bindestreck och punkter.<br><br>Börja och sluta med alfanumeriska tecken. |
> | gallerier/avbildningar/versioner | image | 32-bitars heltal | Siffror och punkter. |
> | images | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slutar med alfanumeriskt eller under streck. |
> | snapshots | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slutar med alfanumeriskt eller under streck. |
> | virtualMachines | Resursgrupp | 1-15 (Windows)<br>1-64 (Linux)<br><br>Se kommentaren nedan. | Kan inte använda:<br> `\/""[]:|<>+=;,?*@&`<br><br>Får inte börja med under streck. Kan inte sluta med punkt eller bindestreck. |
> | virtualMachineScaleSets | Resursgrupp | 1-15 (Windows)<br>1-64 (Linux)<br><br>Se kommentaren nedan. | Kan inte använda:<br> `\/""[]:|<>+=;,?*@&`<br><br>Får inte börja med under streck. Kan inte sluta med punkt eller bindestreck. |

> [!NOTE]
> Virtuella Azure-datorer har två distinkta namn: resurs namn och värdnamn. När du skapar en virtuell dator i portalen används samma värde för båda namnen. Begränsningarna i föregående tabell är för värd namnet. Det faktiska resursnamnet kan innehålla upp till 64 tecken.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | containerGroups | Resursgrupp | 1–63 | Små bokstäver, siffror och bindestreck.<br><br>Får inte börja eller sluta med bindestreck. Efterföljande bindestreck är inte tillåtna. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | register | EAN | 5-50 | Alfanumeriska tecken. |
> | register/buildTasks | registernyckeln | 5-50 | Alfanumeriska tecken. |
> | register/buildTasks/steg | Bygg uppgift | 5-50 | Alfanumeriska tecken. |
> | register/replikeringar | registernyckeln | 5-50 | Alfanumeriska tecken. |
> | register/scopeMaps | registernyckeln | 5-50 | Alfanumeriska tecken, bindestreck och under streck. |
> | register/uppgifter | registernyckeln | 5-50 | Alfanumeriska tecken, bindestreck och under streck. |
> | register/token | registernyckeln | 5-50 | Alfanumeriska tecken, bindestreck och under streck. |
> | register/Webhooks | registernyckeln | 5-50 | Alfanumeriska tecken. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | managedClusters | Resursgrupp | 1–63 | Alfanumeriska tecken, under streck och bindestreck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | openShiftManagedClusters | Resursgrupp | 1-30 | Alfanumeriska tecken. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | NAV | Resursgrupp | 1-64 | Alfanumeriska tecken.<br><br>Börja med en bokstav.  |
> | hubbar/authorizationPolicies | ) | 1-50 | Alfanumeriska tecken, under streck och punkter.<br><br>Börja och sluta med alfanumeriska tecken. |
> | hubbar/anslutningar | ) | 1-128 | Alfanumeriska tecken och under streck.<br><br>Börja med en bokstav. |
> | hubbar/anslutningar/mappningar | anslutningsapp | 1-128 | Alfanumeriska tecken och under streck.<br><br>Börja med en bokstav. |
> | hubbar/interaktioner | ) | 1-128 | Alfanumeriska tecken och under streck.<br><br>Börja med en bokstav. |
> | hubbar/KPI | ) | 1-512 | Alfanumeriska tecken och under streck.<br><br>Börja med en bokstav. |
> | hubbar/länkar | ) | 1-512 | Alfanumeriska tecken och under streck.<br><br>Börja med en bokstav. |
> | hubbar/förutsägelser | ) | 1-512 | Alfanumeriska tecken och under streck.<br><br>Börja med en bokstav. |
> | hubbar/profiler | ) | 1-128 | Alfanumeriska tecken och under streck.<br><br>Börja med en bokstav. |
> | hubbar/relationshipLinks | ) | 1-512 | Alfanumeriska tecken och under streck.<br><br>Börja med en bokstav. |
> | hubbar/relationer | ) | 1-512 | Alfanumeriska tecken och under streck.<br><br>Börja med en bokstav. |
> | hubbar/roleAssignments | ) | 1-128 | Alfanumeriska tecken och under streck.<br><br>Börja med en bokstav. |
> | hubbar/vyer | ) | 1-512 | Alfanumeriska tecken och under streck.<br><br>Börja med en bokstav. |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | typer | Resursgrupp | 1-180 | Kan inte använda:<br>`%&\\?/`<br><br>Kan inte sluta med punkt eller blank steg. |
> | resourceProviders | Resursgrupp | 3-64 | Kan inte använda:<br>`%&\\?/`<br><br>Kan inte sluta med punkt eller blank steg. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Utskrifts | Resursgrupp | 3–24 | Alfanumeriska tecken, bindestreck, under streck och punkter. |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | arbets ytor | Resursgrupp | 3-30 | Alfanumeriska tecken, under streck och bindestreck |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | fabriker | EAN | 3–63 | Alfanumeriska tecken och bindestreck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | fabriker/data flöden | valda | 1-260 | Kan inte använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker/data uppsättningar | valda | 1-260 | Kan inte använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker/integrationRuntimes | valda | 3–63 | Alfanumeriska tecken och bindestreck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | fabriker/linkedservices | valda | 1-260 | Kan inte använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker | valda | 1-260 | Kan inte använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker/utlösare | valda | 1-260 | Kan inte använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker/utlösare/rerunTriggers | utlösare | 1-260 | Kan inte använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | EAN | 3–24 | Små bokstäver och siffror. |
> | konton/computePolicies | konto | 3-60 | Alfanumeriska tecken, bindestreck och under streck. |
> | konton/dataLakeStoreAccounts | konto | 3–24 | Små bokstäver och siffror. |
> | konton/firewallRules | konto | 3-50 | Alfanumeriska tecken, bindestreck och under streck. |
> | konton/storageAccounts | konto | 3-60 | Alfanumeriska tecken, bindestreck och under streck. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | EAN | 3–24 | Små bokstäver och siffror. |
> | konton/firewallRules | konto | 3-50 | Alfanumeriska tecken, bindestreck och under streck. |
> | konton/virtualNetworkRules | konto | 3-50 | Alfanumeriska tecken, bindestreck och under streck. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | services | Resursgrupp | 2-62 | Alfanumeriska tecken, bindestreck, punkter och under streck.<br><br>Börja med alfanumeriskt. |
> | tjänster/projekt | tjänst | 2-57 | Alfanumeriska tecken, bindestreck, punkter och under streck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | brygghuvudservrar | EAN | 3–63 | Små bokstäver, bindestreck och siffror.<br><br>Får inte börja eller sluta med bindestreck. |
> | servrar/databaser | brygghuvudservrar | 1–63 | Alfanumeriska tecken och bindestreck. |
> | servrar/firewallRules | brygghuvudservrar | 1-128 | Alfanumeriska tecken, bindestreck och under streck. |
> | servrar/virtualNetworkRules | brygghuvudservrar | 1-128 | Alfanumeriska tecken och bindestreck. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | brygghuvudservrar | EAN | 3–63 | Små bokstäver, bindestreck och siffror.<br><br>Får inte börja eller sluta med bindestreck. |
> | servrar/databaser | brygghuvudservrar | 1–63 | Alfanumeriska tecken och bindestreck. |
> | servrar/firewallRules | brygghuvudservrar | 1-128 | Alfanumeriska tecken, bindestreck och under streck. |
> | servrar/virtualNetworkRules | brygghuvudservrar | 1-128 | Alfanumeriska tecken och bindestreck. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | brygghuvudservrar | EAN | 3–63 | Små bokstäver, bindestreck och siffror.<br><br>Får inte börja eller sluta med bindestreck. |
> | servrar/databaser | brygghuvudservrar | 1–63 | Alfanumeriska tecken och bindestreck. |
> | servrar/firewallRules | brygghuvudservrar | 1-128 | Alfanumeriska tecken, bindestreck och under streck. |
> | servrar/virtualNetworkRules | brygghuvudservrar | 1-128 | Alfanumeriska tecken och bindestreck. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | IotHubs | EAN | 3-50 | Alfanumeriska tecken och bindestreck.<br><br>Kan inte sluta med bindestreck. |
> | IotHubs/certifikat | IoT Hub | 1-64 | Alfanumeriska tecken, bindestreck, punkter och under streck. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumeriska tecken, bindestreck, punkter och under streck. |
> | provisioningServices | Resursgrupp | 3-64 | Alfanumeriska tecken och bindestreck.<br><br>Sluta med alfanumeriska tecken. |
> | provisioningServices/certifikat | provisioningServices | 1-64 | Alfanumeriska tecken, bindestreck, punkter och under streck. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Laboration | Resursgrupp | 1-50 | Alfanumeriska tecken, under streck och bindestreck. |
> | labb/customimages | test | 1–80 | Alfanumeriska tecken, under streck, bindestreck och parenteser. |
> | labb/formler | test | 1–80 | Alfanumeriska tecken, under streck, bindestreck och parenteser. |
> | labb/virtualmachines | test | 1-15 (Windows)<br>1-64 (Linux) | Alfanumeriska tecken och bindestreck.<br><br>Börja och sluta med alfanumeriska tecken. Får inte vara alla siffror. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | databaseAccounts | EAN | 3-31 | Små bokstäver, siffror och bindestreck.<br><br>Börja med en gemen bokstav eller siffra. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | domäner | Resursgrupp | 3-50 | Alfanumeriska tecken och bindestreck. |
> | domäner/ämnen | domänsuffix | 3-50 | Alfanumeriska tecken och bindestreck. |
> | eventSubscriptions | Resursgrupp | 3-64 | Alfanumeriska tecken och bindestreck. |
> | avsnitt | Resursgrupp | 3-50 | Alfanumeriska tecken och bindestreck. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | kluster | Resursgrupp | 6-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med en bokstav. Slutar med en bokstav eller en siffra. |
> | namn områden | EAN | 6-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med en bokstav. Slutar med en bokstav eller en siffra. |
> | namnrymder/AuthorizationRules | namnområde | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Börja och sluta med en bokstav eller en siffra. |
> | namnrymder/disasterRecoveryConfigs | namnområde | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Börja och sluta med en bokstav eller en siffra. |
> | namnrymder/eventhubs | namnområde | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Börja och sluta med en bokstav eller en siffra. |
> | namnrymder/eventhubs/authorizationRules | händelsehubben | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Börja och sluta med en bokstav eller en siffra. |
> | namnrymder/eventhubs/consumergroups | händelsehubben | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Börja och sluta med en bokstav eller en siffra. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | kluster | EAN | 3-59 | Alfanumeriska tecken och bindestreck<br><br>Börja och sluta med en bokstav eller en siffra. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Utskrifts | Resursgrupp | 2–64 | Alfanumeriska tecken och bindestreck.<br><br>Börja med en bokstav. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | IoTApps | EAN | 2-63 | Små bokstäver, siffror och bindestreck.<br><br>Börja med en gemen bokstav eller siffra. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | valv | EAN | 3–24 | Alfanumeriska tecken och bindestreck.<br><br>Börja med en bokstav. Slutar med en bokstav eller en siffra. Får inte innehålla bindestreck i rad. |
> | valv/hemligheter | Valv | 1-127 | Alfanumeriska tecken och bindestreck. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | kluster | EAN | 4-22 | Små bokstäver och siffror.<br><br>Börja med en bokstav. |
> | /Clusters/databaser | flernodskluster | 1-260 | Alfanumeriska tecken, bindestreck, blank steg och punkter. |
> | /Clusters/databaser/dataConnections | database | 1-40 | Alfanumeriska tecken, bindestreck, blank steg och punkter. |
> | /Clusters/databaser/eventhubconnections | database | 1-40 | Alfanumeriska tecken, bindestreck, blank steg och punkter. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | integrationAccounts | Resursgrupp | 1–80 | Alfanumeriska tecken, bindestreck, under streck, punkter och parenteser. |
> | integrationAccounts/sammansättningar | integrationskonto | 1–80 | Alfanumeriska tecken, bindestreck, under streck, punkter och parenteser. |
> | integrationAccounts / batchConfigurations | integrationskonto | 1-20 | Alfanumeriska tecken. |
> | integrationAccounts/certifikat | integrationskonto | 1–80 | Alfanumeriska tecken, bindestreck, under streck, punkter och parenteser. |
> | integrationAccounts/Maps | integrationskonto | 1–80 | Alfanumeriska tecken, bindestreck, under streck, punkter och parenteser. |
> | integrationAccounts/partners | integrationskonto | 1–80 | Alfanumeriska tecken, bindestreck, under streck, punkter och parenteser. |
> | integrationAccounts / rosettanetprocessconfigurations | integrationskonto | 1–80 | Alfanumeriska tecken, bindestreck, under streck, punkter och parenteser. |
> | integrationAccounts/scheman | integrationskonto | 1–80 | Alfanumeriska tecken, bindestreck, under streck, punkter och parenteser. |
> | integrationAccounts/sessioner | integrationskonto | 1–80 | Alfanumeriska tecken, bindestreck, under streck, punkter och parenteser. |
> | integrationServiceEnvironments | Resursgrupp | 1–80 | Alfanumeriska tecken, bindestreck, punkter och under streck. |
> | integrationServiceEnvironments/managedApis | integrerings tjänst miljö | 1–80 | Alfanumeriska tecken, bindestreck, punkter och under streck. |
> | arbetsflöden | Resursgrupp | 1–80 | Alfanumeriska tecken, bindestreck, under streck, punkter och parenteser. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | commitmentPlans | Resursgrupp | 1-260 | Kan inte använda:<br>`<>*%&:?+/\\`<br><br>Kan inte sluta med ett blank steg. |
> | WebServices | Resursgrupp | 1-260 | Kan inte använda:<br>`<>*%&:?+/\\`<br><br>Kan inte sluta med ett blank steg. |
> | arbets ytor | Resursgrupp | 1-260 | Kan inte använda:<br>`<>*%&:?+/\\`<br><br>Kan inte sluta med ett blank steg. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | arbets ytor | Resursgrupp | 3-33 | Alfanumeriska tecken och bindestreck. |
> | arbets ytor/beräkningar | arbetsyta | 2-16 | Alfanumeriska tecken och bindestreck. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | userAssignedIdentities | Resursgrupp | 3-128 | Alfanumeriska tecken, bindestreck och under streck<br><br>Börja med en bokstav eller en siffra. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | Resursgrupp | 1-98 (för resurs gruppens namn och konto namn) | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Media Services | Resursgrupp | 3–24 | Små bokstäver och siffror. |
> | Media Services/liveEvents | Medie tjänst | 1-32 | Alfanumeriska tecken och bindestreck.<br><br>Börja med alfanumeriskt. |
> | Media Services/liveEvents/liveOutputs | Live-händelse | 1-256 | Alfanumeriska tecken och bindestreck.<br><br>Börja med alfanumeriskt. |
> | Media Services/strömnings slut punkter | Medie tjänst | 1-24 | Alfanumeriska tecken och bindestreck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | applicationGateways | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | applicationSecurityGroups | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | azureFirewalls | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slutar med alfanumeriskt eller under streck. |
> | bastionHosts | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | anslutning | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | dnsZones | Resursgrupp | 1-63 tecken<br><br>2 till 34 etiketter<br><br>Varje etikett är en uppsättning tecken som skiljs åt av en punkt. Till exempel har **contoso.com** 2 etiketter. | Varje etikett får innehålla alfanumeriska tecken, under streck och bindestreck.<br><br>Varje etikett avgränsas med en punkt. |
> | expressRouteCircuits | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | firewallPolicies | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | firewallPolicies / ruleGroups | Brand Väggs princip | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | frontDoors | EAN | 5-64 | Alfanumeriska tecken och bindestreck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | Belastningsutjämnare | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | Belastningsutjämnare/inboundNatRules | belastningsutjämnare | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | localNetworkGateways | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | networkInterfaces | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | networkSecurityGroups | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | networkSecurityGroups/securityRules | nätverks säkerhets grupp | 1–80 |  Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | networkWatchers | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | privateDnsZones | Resursgrupp | 1-63 tecken<br><br>2 till 34 etiketter<br><br>Varje etikett är en uppsättning tecken som skiljs åt av en punkt. Till exempel har **contoso.com** 2 etiketter. | Varje etikett får innehålla alfanumeriska tecken, under streck och bindestreck.<br><br>Varje etikett avgränsas med en punkt. |
> | privateDnsZones / virtualNetworkLinks | privat DNS-zon | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | publicIPAddresses | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | publicIPPrefixes | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | routeFilters | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | routeFilters / routeFilterRules | flödesfilter | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | routeTables | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | routeTables/vägar | route table | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | serviceEndpointPolicies | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | trafficmanagerprofiles | EAN | 1–63 | Alfanumeriska tecken, bindestreck och punkter.<br><br>Börja och sluta med alfanumeriska tecken. |
> | virtualNetworkGateways | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | virtualNetworks | Resursgrupp | 2–64 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | virtualnetworks/undernät | virtuellt nätverk | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | virtualNetworks/virtualNetworkPeerings | virtuellt nätverk | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | virtualWans | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | vpnGateways | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | vpnGateways / vpnConnections | VPN gateway | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |
> | vpnSites | Resursgrupp | 1–80 | Alfanumeriska tecken, under streck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Slut alfanumeriskt eller under streck. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | namn områden | EAN | 6-50 | Alfanumeriska tecken och bindestreck<br><br>Börja och sluta med alfanumeriska tecken. |
> | namnrymder/AuthorizationRules | namnområde | 1-256 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Starta alfanumeriskt. |
> | namnrymder/notificationHubs | namnområde | 1-260 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Starta alfanumeriskt. |
> | namnrymder/notificationHubs/AuthorizationRules | Notification Hub | 1-256 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Starta alfanumeriskt. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | kluster | Resursgrupp | 4-63 | Alfanumeriska tecken och bindestreck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | arbets ytor | Resursgrupp | 4-63 | Alfanumeriska tecken och bindestreck.<br><br>Börja och sluta med alfanumeriska tecken. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | instrument paneler | Resursgrupp | 3-160 | Alfanumeriska tecken och bindestreck.<br><br>Om du vill använda begränsade tecken lägger du till en tagg med namnet **dold rubrik** med namnet på instrument panelen som du vill använda. Portalen visar det namnet när instrument panelen visas. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | workspaceCollections | regionen | 3–63 | Alfanumeriska tecken och bindestreck.<br><br>Får inte börja med bindestreck. Det går inte att använda efterföljande bindestreck. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | kapaciteter | regionen | 3–63 | Små bokstäver eller siffror<br><br>Börja med gemener. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | valv | Resursgrupp | 2-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med en bokstav. |
> | valv/backupPolicies | valv | 3-150 | Alfanumeriska tecken och bindestreck.<br><br>Börja med en bokstav. Kan inte sluta med bindestreck. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | namn områden | EAN | 6-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med en bokstav. Sluta med en bokstav eller en siffra. |
> | namnrymder/AuthorizationRules | namnområde | 1-50 |  Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | namnrymder/HybridConnections | namnområde | 1-260 | Alfanumeriska tecken, punkter, bindestreck, under streck och snedstreck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | namnrymder/HybridConnections/authorizationRules | Hybrid anslutning | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | namnrymder/WcfRelays | namnområde | 1-260 | Alfanumeriska tecken, punkter, bindestreck, under streck och snedstreck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | namnrymder/WcfRelays/authorizationRules | WCF-relä | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Börja och sluta med alfanumeriska tecken. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | distributioner | Resursgrupp | 1-64 | Alfanumeriska tecken, under streck, parenteser, bindestreck och punkter. |
> | ResourceGroups | prenumeration | 1–90 | Alfanumeriska tecken, under streck, parenteser, bindestreck, punkter och Unicode-tecken som matchar regex- [dokumentationen](/rest/api/resources/resourcegroups/createorupdate).<br><br>Kan inte sluta med punkt. |
> | tagNames | resource | 1-512 | Kan inte använda:<br>`<>%&\?/` |
> | tagNames / tagValues | Taggnamn | 1-256 | Alla tecken. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | namn områden | EAN | 6-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med en bokstav. Sluta med en bokstav eller en siffra.<br><br>Mer information finns i [skapa namn område](/rest/api/servicebus/create-namespace). |
> | namnrymder/AuthorizationRules | namnområde | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Starta och sluta med alphnumeric. |
> | namnrymder/disasterRecoveryConfigs | EAN | 6-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med en bokstav. Sluta med alfanumeriska tecken. |
> | namnrymder/migrationConfigurations | namnområde |  | Bör alltid vara **$default**. |
> | namnrymder/köer | namnområde | 1-260 | Alfanumeriska tecken, punkter, bindestreck, under streck och snedstreck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | namnrymder/köer/authorizationRules | kö | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Starta och sluta med alphnumeric. |
> | namn områden/ämnen | namnområde | 1-260 | Alfanumeriska tecken, punkter, bindestreck, under streck och snedstreck.<br><br>Börja och sluta med alfanumeriska tecken. |
> | namnrymder/ämnen/authorizationRules | ämne | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Starta och sluta med alphnumeric. |
> | namnrymder/ämnen/prenumerationer | ämne | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Starta och sluta med alphnumeric. |
> | namn områden/ämnen/prenumerationer/regler | prenumeration | 1-50 | Alfanumeriska tecken, punkter, bindestreck och under streck.<br><br>Starta och sluta med alphnumeric. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | kluster | regionen | 4-23 | Små bokstäver, siffror och bindestreck.<br><br>Börja med gemener. Slutar med bokstav eller siffra i gemener. |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | signalR | EAN | 3–63 | Alfanumeriska tecken och bindestreck.<br><br>Börja med en bokstav. Slutar med en bokstav eller en siffra.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | managedInstances | EAN | 1–63 | Små bokstäver, siffror och bindestreck.<br><br>Får inte börja eller sluta med bindestreck. |
> | brygghuvudservrar | EAN | 1–63 | Små bokstäver, siffror och bindestreck.<br><br>Får inte börja eller sluta med bindestreck. |
> | servrar/databaser | server | 1-128 | Kan inte använda:<br>`<>*%&:\/?`<br><br>Kan inte sluta med punkt eller blank steg. |
> | servrar/databaser/syncGroups | database | 1-150 | Alfanumeriska tecken, bindestreck och under streck. |
> | servrar/elasticPools | server | 1-128 | Kan inte använda:<br>`<>*%&:\/?`<br><br>Kan inte sluta med punkt eller blank steg. |
> | servrar/failoverGroups | EAN | 1–63 | Små bokstäver, siffror och bindestreck.<br><br>Får inte börja eller sluta med bindestreck. |
> | servrar/firewallRules | server | 1-128 | Kan inte använda:<br>`<>*%&:;\/?`<br><br>Kan inte sluta med punkt. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | storageAccounts | EAN | 3–24 | Små bokstäver och siffror. |
> | storageAccounts/blobServices | storage account |  | Måste vara `default`. |
> | storageAccounts/blobServices/containers | storage account | 3–63 | Små bokstäver, siffror och bindestreck.<br><br>Börja med en gemen bokstav eller siffra. Det går inte att använda efterföljande bindestreck. |
> | storageAccounts/fileServices | storage account |  | Måste vara `default`. |
> | storageAccounts/fileServices/resurser | storage account | 3–63 | Små bokstäver, siffror och bindestreck.<br><br>Får inte börja eller sluta med bindestreck. Det går inte att använda efterföljande bindestreck. |
> | storageAccounts/managementPolicies | storage account |  | Måste vara `default`. |
> | blob | container | 1–1024 | Alla URL-tecken, SKIFT läges känsliga |
> | kö | storage account | 3–63 | Små bokstäver, siffror och bindestreck.<br><br>Får inte börja eller sluta med bindestreck. Det går inte att använda efterföljande bindestreck. |
> | table | storage account | 3–63 | Alfanumeriska tecken.<br><br>Börja med en bokstav. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | storageSyncServices | Resursgrupp | 1-260 | Alfanumeriska tecken, blank steg, punkter, bindestreck och under streck.<br><br>Kan inte sluta med punkt eller blank steg. |
> | storageSyncServices / syncGroups | tjänsten för synkronisering av lagring | 1-260 | Alfanumeriska tecken, blank steg, punkter, bindestreck och under streck.<br><br>Kan inte sluta med punkt eller blank steg. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | hantera | Resursgrupp | 2-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med en bokstav. Sluta med alfanumeriska tecken. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | streamingjobs | Resursgrupp | 3–63 | Alfanumeriska tecken, bindestreck och under streck. |
> | streamingjobs/Functions | strömmande jobb | 3–63 | Alfanumeriska tecken, bindestreck och under streck. |
> | streamingjobs/indata | strömmande jobb | 3–63 | Alfanumeriska tecken, bindestreck och under streck. |
> | streamingjobs/utdata | strömmande jobb | 3–63 | Alfanumeriska tecken, bindestreck och under streck. |
> | streamingjobs/transformeringar | strömmande jobb | 3–63 | Alfanumeriska tecken, bindestreck och under streck. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | utrymmen | Resursgrupp | 1–90 | Kan inte använda:<br>`'<>%&:\?/#` |
> | miljöer/accessPolicies | miljö | 1–90 | Kan inte använda:<br> `'<>%&:\?/#` |
> | miljöer/eventSources | miljö | 1–90 | Kan inte använda:<br>`'<>%&:\?/#` |
> | miljöer/referenceDataSets | miljö | 3–63 | Alfanumeriska tecken |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Server grupper | Resursgrupp | 1-40 | Alfanumeriska tecken och bindestreck. |
> | stationer | EAN | 2-60 | Innehåller alfanumeriska tecken och bindestreck.<br><br>Får inte börja eller sluta med bindestreck. |
> | platser/platser | plats | 2-59 | Alfanumeriska tecken och bindestreck. |

## <a name="next-steps"></a>Nästa steg

Rekommendationer om hur du namnger resurser finns i [Ready: rekommenderade namngivnings-och taggnings konventioner](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
