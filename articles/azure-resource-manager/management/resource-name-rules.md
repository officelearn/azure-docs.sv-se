---
title: Begränsningar för namngivning av resurser
description: Visar regler och begränsningar för att namnge Azure-resurser.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: fab1ab2bb779b3826c852e49da7970030d34594d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086393"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Namngivningsregler och begränsningar för Azure-resurser

Den här artikeln sammanfattar namngivningsregler och begränsningar för Azure-resurser. Rekommendationer om hur du namnger resurser finns i [Rekommenderade namngivnings- och taggningskonventioner](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

Resursnamn är skiftlägesokänsliga om de inte uttryckligen anges i kolumnen giltiga tecken.

I följande tabeller refererar termen alfanumerisk till:

* **a** till **z** (gemener)
* **A** till **Z** (versaler)
* **0** till **9** (siffror)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Servrar | Resursgrupp | 3–63 | Gemener och siffror.<br><br>Börja med gemener. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiHanagement

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | tjänst | Globala | 1-50 | Alfanumerika.<br><br>Börja med bokstaven. |
> | tjänst / apis | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / apis / frågor | api | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / apis / frågor / bilagor | Frågan | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / apis / frågor / kommentarer | Frågan | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / apis / operationer | api | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / apis / operationer / taggar | Drift | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / apis / utgåvor | api | 1–80 | Alfanumeriska, understreck och bindestreck.<br><br>Börja och sluta med alfanumeriskt eller understreck. |
> | service / apis / scheman | api | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst / apis / tagDescriptions | api | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst / apis / taggar | api | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst / api-version-set | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / auktoriseringServer | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / backends | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / certifikat | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / diagnostik | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / grupper | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / grupper / användare | grupp | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst / identitetProviders | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst / loggers | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / meddelanden | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / meddelanden / mottagareEmails | avisering | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / openidConnectProviders | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / policyer | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / produkter | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / produkter / apis | produkt | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / produkter / grupper | produkt | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / produkter / taggar | produkt | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / egenskaper | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst / abonnemang | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / taggar | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | service / mallar | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst / användare | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppKonfigurering

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | configurationStores | Resursgrupp | 5-50 | Alfanumeriska, understreck och bindestreck. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Lås | tilldelningens omfattning | 1–90 | Alfanumeriska, punkter, understreck, bindestreck och parentes.<br><br>Kan inte sluta i punkt. |
> | policytilldelningar | tilldelningens omfattning | 1-128 visningsnamn<br><br>Resursnamn 1-260 | Visningsnamnet kan innehålla alla tecken.<br><br>Resursnamnet kan inte `%` inkludera och kan inte sluta med period eller blanksteg. |
> | policydefinitioner | definitionsområdet | 1-128 visningsnamn<br><br>Resursnamn 1-260 | Visningsnamnet kan innehålla alla tecken.<br><br>Resursnamnet kan inte `%` inkludera och kan inte sluta med period eller blanksteg. |
> | policySetDefinitions | definitionsområdet | 1-128 visningsnamn<br><br>Resursnamn 1-260 | Visningsnamnet kan innehålla alla tecken.<br><br>Resursnamnet kan inte `%` inkludera och kan inte sluta med period eller blanksteg.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | automationKonton | Resursgrupp | 6-50 | Alfanumeriska och bindestreck.<br><br>Börja med bokstaven och sluta med alfanumeriskt. |
> | automationKonton / certifikat | automation konto | 1-128 | Det går inte att använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med rymden.  |
> | automationKonton / anslutningar | automation konto | 1-128 | Det går inte att använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med rymden. |
> | automationKonton / autentiseringsuppgifter | automation konto | 1-128 | Det går inte att använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med rymden. |
> | automationKonton / runbooks | automation konto | 1–63 | Alfanumeriska, understreck och bindestreck.<br><br>Börja med bokstaven.  |
> | automationKonton / scheman | automation konto | 1-128 | Det går inte att använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med rymden. |
> | automationKonton / variabler | automation konto | 1-128 | Det går inte att använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med rymden. |
> | automationKonton / watchers | automation konto | 1–63 |  Alfanumeriska, understreck och bindestreck.<br><br>Börja med bokstaven. |
> | automationKonton / webhooks | automation konto | 1-128 | Det går inte att använda:<br> `<>*%&:\?.+/` <br><br>Kan inte sluta med rymden. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | batchKonton | Region | 3–24 | Gemener och siffror. |
> | batchKonton / applikationer | batch-konto | 1-64 | Alfanumeriska, understreck och bindestreck. |
> | batchKonton / certifikat | batch-konto | 5-45 | Alfanumeriska, understreck och bindestreck. |
> | batchKonton / pooler | batch-konto | 1-64 | Alfanumeriska, understreck och bindestreck. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain (på andra)

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | blockchainMembers | Globala | 2-20 | Gemener och siffror.<br><br>Börja med gemener. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | botServices | Globala | 2–64 |  Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |
> | botServices / kanaler | bot tjänst | 2–64 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |
> | botServices / Anslutningar | bot tjänst | 2–64 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |
> | företagKanaler | Resursgrupp | 2–64 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Redis | Globala | 1–63 | Alfanumeriska och bindestreck.<br><br>Börja och sluta med alfanumeriskt. Det är inte tillåtet att ta på varandra följande bindestreck. |
> | Redis / firewallRules | Redis | 1-256 | Alfanumerika |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Profiler | Resursgrupp | 1-260 | Alfanumeriska och bindestreck.<br><br>Börja och sluta med alfanumeriskt. |
> | profiler/slutpunkter | Globala | 1-50 | Alfanumeriska och bindestreck.<br><br>Börja och sluta med alfanumeriskt. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | certifikatOrdar | Resursgrupp | 3-30 | Alfanumerika. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | Resursgrupp | 2–64 | Alfanumeriska och bindestreck.<br><br>Börja och sluta med alfanumeriskt. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | tillgänglighetSeter | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta med alfanumeriskt eller understreck. |
> | diskEncryptionSets | Resursgrupp | 1–80 | Alfanumeriska och understreck. |
> | Diskar | Resursgrupp | 1–80 | Alfanumeriska och understreck. |
> | Gallerier | Resursgrupp | 1–80 | Alfanumeriska och punkter.<br><br>Börja och sluta med alfanumeriskt. |
> | gallerier / applikationer | galleri | 1–80 | Alfanumeriska, bindestreck och punkter.<br><br>Börja och sluta med alfanumeriskt. |
> | gallerier / applikationer / versioner | program | 32-bitars heltal | Siffror och punkter. |
> | gallerier / bilder | galleri | 1–80 | Alfanumeriska, bindestreck och punkter.<br><br>Börja och sluta med alfanumeriskt. |
> | gallerier / bilder / versioner | image | 32-bitars heltal | Siffror och punkter. |
> | images | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta med alfanumeriskt eller understreck. |
> | snapshots | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta med alfanumeriskt eller understreck. |
> | virtuellaMaskiner | Resursgrupp | 1–15 (Windows)<br>1-64 (Linux)<br><br>Se anmärkning nedan. | Det går inte att använda:<br> `\/""[]:|<>+=;,?*@&`<br><br>Kan inte börja med understreck. Det går inte att sluta med punkt eller bindestreck. |
> | virtuellaMachineScaleSets | Resursgrupp | 1–15 (Windows)<br>1-64 (Linux)<br><br>Se anmärkning nedan. | Det går inte att använda:<br> `\/""[]:|<>+=;,?*@&`<br><br>Kan inte börja med understreck. Det går inte att sluta med punkt eller bindestreck. |

> [!NOTE]
> Virtuella Azure-datorer har två olika namn: resursnamn och värdnamn. När du skapar en virtuell dator i portalen används samma värde för båda namnen. Begränsningarna i tabellen föregående är för värdnamnet. Det faktiska resursnamnet kan innehålla upp till 64 tecken.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | containerGroups | Resursgrupp | 1–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att starta eller sluta med bindestreck. Flera bindestreck efter varandra tillåts inte. |

## <a name="microsoftcontainerregistry"></a>Microsoft.Container-registret

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Register | Globala | 5-50 | Alfanumerika. |
> | register / buildTasks | registry | 5-50 | Alfanumerika. |
> | register / buildTasks/steps | skapa uppgift | 5-50 | Alfanumerika. |
> | register /replikeringar | registry | 5-50 | Alfanumerika. |
> | register / scopeMaps | registry | 5-50 | Alfanumeriska, bindestreck och understreck. |
> | register /uppgifter | registry | 5-50 | Alfanumeriska, bindestreck och understreck. |
> | register / tokens | registry | 5-50 | Alfanumeriska, bindestreck och understreck. |
> | register / webhooks | registry | 5-50 | Alfanumerika. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | managedClusters | Resursgrupp | 1–63 | Alfanumeriska, understreck och bindestreck.<br><br>Börja och sluta med alfanumeriskt. |
> | openShiftManagedClusters | Resursgrupp | 1-30 | Alfanumerika. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | nav | Resursgrupp | 1-64 | Alfanumerika.<br><br>Börja med bokstaven.  |
> | nav / tillståndPolicies | hubben | 1-50 | Alfanumeriskt, understreck och punkter.<br><br>Börja och sluta med alfanumeriskt. |
> | nav / kontakter | hubben | 1-128 | Alfanumeriska och understreck.<br><br>Börja med bokstaven. |
> | nav / kopplingar/mappningar | anslutningsapp | 1-128 | Alfanumeriska och understreck.<br><br>Börja med bokstaven. |
> | nav / interaktioner | hubben | 1-128 | Alfanumeriska och understreck.<br><br>Börja med bokstaven. |
> | nav / kpi | hubben | 1-512 | Alfanumeriska och understreck.<br><br>Börja med bokstaven. |
> | nav / länkar | hubben | 1-512 | Alfanumeriska och understreck.<br><br>Börja med bokstaven. |
> | nav / förutsägelser | hubben | 1-512 | Alfanumeriska och understreck.<br><br>Börja med bokstaven. |
> | nav / profiler | hubben | 1-128 | Alfanumeriska och understreck.<br><br>Börja med bokstaven. |
> | nav / relationshipLinks | hubben | 1-512 | Alfanumeriska och understreck.<br><br>Börja med bokstaven. |
> | nav / relationer | hubben | 1-512 | Alfanumeriska och understreck.<br><br>Börja med bokstaven. |
> | nav / rollTilldelningar | hubben | 1-128 | Alfanumeriska och understreck.<br><br>Börja med bokstaven. |
> | nav / vyer | hubben | 1-512 | Alfanumeriska och understreck.<br><br>Börja med bokstaven. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Sammanslutningar | Resursgrupp | 1-180 | Det går inte att använda:<br>`%&\\?/`<br><br>Det går inte att sluta med punkt eller blanksteg. |
> | resurserProviders | Resursgrupp | 3-64 | Det går inte att använda:<br>`%&\\?/`<br><br>Det går inte att sluta med punkt eller blanksteg. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Jobb | Resursgrupp | 3–24 | Alfanumeriska, bindestreck, understreck och punkter. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | arbetsytor | Resursgrupp | 3-30 | Alfanumeriska, understreck och bindestreck |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Fabriker | Globala | 3–63 | Alfanumeriska och bindestreck.<br><br>Börja och sluta med alfanumeriskt. |
> | fabriker/ dataflöden | Fabriken | 1-260 | Det går inte att använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker / dataset | Fabriken | 1-260 | Det går inte att använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker / integrationRuntimes | Fabriken | 3–63 | Alfanumeriska och bindestreck.<br><br>Börja och sluta med alfanumeriskt. |
> | fabriker / länkade tjänster | Fabriken | 1-260 | Det går inte att använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker/ rörledningar | Fabriken | 1-260 | Det går inte att använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker / triggers | Fabriken | 1-260 | Det går inte att använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker / triggers / rerunTriggers | Utlösa | 1-260 | Det går inte att använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | Globala | 3–24 | Gemener och siffror. |
> | konton / computePolicies | konto | 3-60 | Alfanumeriska, bindestreck och understreck. |
> | konton / dataLakeStoreAccounts | konto | 3–24 | Gemener och siffror. |
> | konton / firewallRules | konto | 3-50 | Alfanumeriska, bindestreck och understreck. |
> | konton / lagringKonton | konto | 3-60 | Alfanumeriska, bindestreck och understreck. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | Globala | 3–24 | Gemener och siffror. |
> | konton / firewallRules | konto | 3-50 | Alfanumeriska, bindestreck och understreck. |
> | konton / virtualNetworkRules | konto | 3-50 | Alfanumeriska, bindestreck och understreck. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | services | Resursgrupp | 2-62 | Alfanumeriska, bindestreck, punkter och understreck.<br><br>Börja med alfanumeriskt. |
> | tjänster /projekt | tjänst | 2-57 | Alfanumeriska, bindestreck, punkter och understreck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Servrar | Globala | 3–63 | Gemener, bindestreck och siffror.<br><br>Det går inte att starta eller sluta med bindestreck. |
> | servrar / databaser | Servrar | 1–63 | Alfanumeriska och bindestreck. |
> | servrar / firewallRules | Servrar | 1-128 | Alfanumeriska, bindestreck och understreck. |
> | servrar / virtualNetworkRules | Servrar | 1-128 | Alfanumeriska och bindestreck. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Servrar | Globala | 3–63 | Gemener, bindestreck och siffror.<br><br>Det går inte att starta eller sluta med bindestreck. |
> | servrar / databaser | Servrar | 1–63 | Alfanumeriska och bindestreck. |
> | servrar / firewallRules | Servrar | 1-128 | Alfanumeriska, bindestreck och understreck. |
> | servrar / virtualNetworkRules | Servrar | 1-128 | Alfanumeriska och bindestreck. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Servrar | Globala | 3–63 | Gemener, bindestreck och siffror.<br><br>Det går inte att starta eller sluta med bindestreck. |
> | servrar / databaser | Servrar | 1–63 | Alfanumeriska och bindestreck. |
> | servrar / firewallRules | Servrar | 1-128 | Alfanumeriska, bindestreck och understreck. |
> | servrar / virtualNetworkRules | Servrar | 1-128 | Alfanumeriska och bindestreck. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | IotHubs | Globala | 3-50 | Alfanumeriska och bindestreck.<br><br>Kan inte sluta med bindestreck. |
> | IotHubs / certifikat | IoT-hubb | 1-64 | Alfanumeriska, bindestreck, punkter och understreck. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumeriska, bindestreck, punkter och understreck. |
> | etableringTjänster | Resursgrupp | 3-64 | Alfanumeriska och bindestreck.<br><br>Avsluta med alfanumeriskt. |
> | etableringTjänster/certifikat | etableringTjänster | 1-64 | Alfanumeriska, bindestreck, punkter och understreck. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Labs | Resursgrupp | 1-50 | Alfanumeriska, understreck och bindestreck. |
> | laboratorier / customimages | Lab | 1–80 | Alfanumeriska, understreck, bindestreck och parenteser. |
> | laboratorier / formler | Lab | 1–80 | Alfanumeriska, understreck, bindestreck och parenteser. |
> | laboratorier / virtualmachines | Lab | 1–15 (Windows)<br>1-64 (Linux) | Alfanumeriska och bindestreck.<br><br>Börja och sluta med alfanumeriskt. Det kan inte vara alla siffror. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | databasKonton | Globala | 3-31 | Gemener, siffror och bindestreck.<br><br>Börja med gemener eller tal. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Domäner | Resursgrupp | 3-50 | Alfanumeriska och bindestreck. |
> | domäner / ämnen | domän | 3-50 | Alfanumeriska och bindestreck. |
> | händelseAbonnemang | Resursgrupp | 3-64 | Alfanumeriska och bindestreck. |
> | Ämnen | Resursgrupp | 3-50 | Alfanumeriska och bindestreck. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kluster | Resursgrupp | 6-50 | Alfanumeriska och bindestreck.<br><br>Börja med bokstaven. Avsluta med bokstav eller nummer. |
> | Namnområden | Globala | 6-50 | Alfanumeriska och bindestreck.<br><br>Börja med bokstaven. Avsluta med bokstav eller nummer. |
> | namnområden / AuthorizationRules | namnområde | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med bokstav eller nummer. |
> | namnrymdor / katastrofRecoveryConfigs | namnområde | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med bokstav eller nummer. |
> | namnområden / eventhubs | namnområde | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med bokstav eller nummer. |
> | namnrymd / eventhubs / authorizationRules | händelsehubb | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med bokstav eller nummer. |
> | namnområden / eventhubs / konsumentgrupper | händelsehubb | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med bokstav eller nummer. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kluster | Globala | 3-59 | Alfanumeriska och bindestreck<br><br>Börja och sluta med bokstav eller nummer. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Jobb | Resursgrupp | 2–64 | Alfanumeriska och bindestreck.<br><br>Börja med bokstaven. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Komponenter | Resursgrupp | 1-260 | Det går inte att använda:<br>`%&\?/` <br><br>Det går inte att sluta med mellanslag eller punkt.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | IoTApps | Globala | 2-63 | Gemener, siffror och bindestreck.<br><br>Börja med gemener eller tal. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Valv | Globala | 3–24 | Alfanumeriska och bindestreck.<br><br>Börja med bokstaven. Avsluta med bokstav eller siffra. Det går inte att innehålla på varandra följande bindestreck. |
> | valv / hemligheter | Valv | 1-127 | Alfanumeriska och bindestreck. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kluster | Globala | 4-22 | Gemener och siffror.<br><br>Börja med bokstaven. |
> | /kluster / databaser | cluster | 1-260 | Alfanumeriska, bindestreck, blanksteg och punkter. |
> | /kluster / databaser / dataAnslutningar | databas | 1-40 | Alfanumeriska, bindestreck, blanksteg och punkter. |
> | /kluster / databaser / eventhubconnections | databas | 1-40 | Alfanumeriska, bindestreck, blanksteg och punkter. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | integrationKonton | Resursgrupp | 1–80 | Alfanumeriska, bindestreck, understreck, punkter och parentes. |
> | integrationKonton/sammansättningar | integrationskonto | 1–80 | Alfanumeriska, bindestreck, understreck, punkter och parentes. |
> | integrationKonto/batchKonfigurationer | integrationskonto | 1-20 | Alfanumerika. |
> | integrationRedovisning/certifikat | integrationskonto | 1–80 | Alfanumeriska, bindestreck, understreck, punkter och parentes. |
> | integrationKonton / kartor | integrationskonto | 1–80 | Alfanumeriska, bindestreck, understreck, punkter och parentes. |
> | integrationKonton/partner | integrationskonto | 1–80 | Alfanumeriska, bindestreck, understreck, punkter och parentes. |
> | integrationAktörer / rosettanetprocesskonfigurationer | integrationskonto | 1–80 | Alfanumeriska, bindestreck, understreck, punkter och parentes. |
> | integrationKonto/scheman | integrationskonto | 1–80 | Alfanumeriska, bindestreck, understreck, punkter och parentes. |
> | integrationKonton /sessioner | integrationskonto | 1–80 | Alfanumeriska, bindestreck, understreck, punkter och parentes. |
> | integrationTjänstMiljöer | Resursgrupp | 1–80 | Alfanumeriska, bindestreck, punkter och understreck. |
> | integrationServiceMiljöer / managedApis | integration tjänst miljö | 1–80 | Alfanumeriska, bindestreck, punkter och understreck. |
> | Arbetsflöden | Resursgrupp | 1–80 | Alfanumeriska, bindestreck, understreck, punkter och parentes. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLärning

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | åtagandePlaner | Resursgrupp | 1-260 | Det går inte att använda:<br>`<>*%&:?+/\\`<br><br>Kan inte sluta med ett mellanslag. |
> | Webservices | Resursgrupp | 1-260 | Det går inte att använda:<br>`<>*%&:?+/\\`<br><br>Kan inte sluta med ett mellanslag. |
> | arbetsytor | Resursgrupp | 1-260 | Det går inte att använda:<br>`<>*%&:?+/\\`<br><br>Kan inte sluta med ett mellanslag. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | arbetsytor | Resursgrupp | 3-33 | Alfanumeriska och bindestreck. |
> | arbetsytor / beräkningar | arbetsyta | 2-16 | Alfanumeriska och bindestreck. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | userAssignedId entiteter | Resursgrupp | 3-128 | Alfanumeriska, bindestreck och understreck<br><br>Börja med bokstav eller nummer. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | Resursgrupp | 1-98 (för resursgruppnamn och kontonamn) | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | medietjänster | Resursgrupp | 3–24 | Gemener och siffror. |
> | mediaservices / liveEvents | Medietjänst | 1-32 | Alfanumeriska och bindestreck.<br><br>Börja med alfanumeriskt. |
> | mediaservices / liveEvents / liveOutputs | Live-evenemang | 1-256 | Alfanumeriska och bindestreck.<br><br>Börja med alfanumeriskt. |
> | mediatjänster / streamingEndpoints | Medietjänst | 1-24 | Alfanumeriska och bindestreck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | ansökanGateways | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | applicationSecurityGroups | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | azureFirewalls | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta med alfanumeriskt eller understreck. |
> | bastionHosts (bastionHosts) | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | Anslutningar | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | dnsZones (1) | Resursgrupp | 1-63 tecken<br><br>2 till 34 etiketter<br><br>Varje etikett är en uppsättning tecken avgränsade med en punkt. Till exempel har **contoso.com** 2 etiketter. | Varje etikett kan innehålla alfanumeriska, understreck och bindestreck.<br><br>Varje etikett avgränsas med en punkt. |
> | expressRouteCirklar | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | brandväggPolicies | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | firewallPolicies / ruleGroups | brandväggsprincip | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | frontDosor | Globala | 5-64 | Alfanumeriska och bindestreck.<br><br>Börja och sluta med alfanumeriskt. |
> | loadBalancers | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | loadBalancers / inboundNatRules | belastningsutjämnare | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | localNetworkGateways | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | nätverkGränssnitt | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | nätverkSäkerhetsgrupper | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | nätverkSäkerhetsgrupper /securityRules | nätverkssäkerhetsgrupp | 1–80 |  Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | nätverkWatchers | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | privatDnsZones | Resursgrupp | 1-63 tecken<br><br>2 till 34 etiketter<br><br>Varje etikett är en uppsättning tecken avgränsade med en punkt. Till exempel har **contoso.com** 2 etiketter. | Varje etikett kan innehålla alfanumeriska, understreck och bindestreck.<br><br>Varje etikett avgränsas med en punkt. |
> | privateDnsZones / virtualNetworkLinks | privat DNS-zon | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | offentliga IP-adresser | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | offentligaIP-korrigeringar | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | routeFilters | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | routeFilter/routeFilterRules | ruttfilter | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | routeTables | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | routeTables /rutter | route table | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | serviceEndpointPolicies | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | trafficmanagerprofiles | Globala | 1–63 | Alfanumeriska, bindestreck och punkter.<br><br>Börja och sluta med alfanumeriskt. |
> | virtualNetworkGateways | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | virtualNetworks | Resursgrupp | 2–64 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | virtualnetworks / undernät | virtuellt nätverk | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | virtualNetworks / virtualNetworkPeerings | virtuellt nätverk | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | virtuellaWans | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | vpnGateways (vpnGateways) | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | vpnGateways / vpnAnslutningar | VPN gateway | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | vpnSites (vpnSites) | Resursgrupp | 1–80 | Alfanumeriska, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Namnområden | Globala | 6-50 | Alfanumeriska och bindestreck<br><br>Börja och sluta med alfanumeriskt. |
> | namnområden / AuthorizationRules | namnområde | 1-256 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Starta alfanumeriskt. |
> | namnområden / anmälanHubs | namnområde | 1-260 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Starta alfanumeriskt. |
> | namnområden / anmälanHubs / AuthorizationRules | meddelandehubb | 1-256 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Starta alfanumeriskt. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kluster | Resursgrupp | 4-63 | Alfanumeriska och bindestreck.<br><br>Börja och sluta med alfanumeriskt. |
> | arbetsytor | Resursgrupp | 4-63 | Alfanumeriska och bindestreck.<br><br>Börja och sluta med alfanumeriskt. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | lösningar | arbetsyta | Ej tillämpligt | För lösningar som skapats av Microsoft måste namnet finnas i mönstret:<br>`SolutionType(WorkspaceName)`<br><br>För lösningar som skapats av tredje part måste namnet finnas i mönstret:<br>`SolutionType[WorkspaceName]`<br><br>Ett giltigt namn är till exempel:<br>`AntiMalware(contoso-IT)`<br><br>Lösningstypen är skiftlägeskänslig. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | instrumentpaneler | Resursgrupp | 3-160 | Alfanumeriska och bindestreck.<br><br>Om du vill använda begränsade tecken lägger du till en tagg med namnet **dold rubrik** med det instrumentpanelsnamn som du vill använda. Portalen visar det namnet när instrumentpanelen visas. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | arbetsytaInsamlingar | regionen | 3–63 | Alfanumeriska och bindestreck.<br><br>Kan inte börja med bindestreck. Det går inte att använda på varandra följande bindestreck. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kapacitet | regionen | 3–63 | Gemener eller siffror<br><br>Börja med gemener. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Valv | Resursgrupp | 2-50 | Alfanumeriska och bindestreck.<br><br>Börja med bokstaven. |
> | valv / backupPolicies | valv | 3-150 | Alfanumeriska och bindestreck.<br><br>Börja med bokstaven. Kan inte sluta med bindestreck. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Namnområden | Globala | 6-50 | Alfanumeriska och bindestreck.<br><br>Börja med en bokstav. Avsluta med en bokstav eller ett nummer. |
> | namnområden / AuthorizationRules | namnområde | 1-50 |  Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med alfanumeriskt. |
> | namnområden / HybridAnslutningar | namnområde | 1-260 | Alfanumeriska, punkter, bindestreck, understreck och snedstreck.<br><br>Börja och sluta med alfanumeriskt. |
> | namnområden / HybridConnections/authorizationRules | hybridanslutning | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med alfanumeriskt. |
> | namnområden / WcfRelays | namnområde | 1-260 | Alfanumeriska, punkter, bindestreck, understreck och snedstreck.<br><br>Börja och sluta med alfanumeriskt. |
> | namnområden / WcfRelays / authorizationRules | Wcf relä | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med alfanumeriskt. |

## <a name="microsoftresources"></a>Microsoft.Resurser

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Distributioner | Resursgrupp | 1-64 | Alfanumeriska, understreck, parenteser, bindestreck och punkter. |
> | resursgrupper | prenumeration | 1–90 | Alfanumeriska tecken, understreck, parenteser, bindestreck, punkter och unicode-tecken som matchar [regex-dokumentationen](/rest/api/resources/resourcegroups/createorupdate).<br><br>Kan inte sluta med punkt. |
> | tagNames | resource | 1-512 | Det går inte att använda:<br>`<>%&\?/` |
> | tagNames / tagValues | taggnamn | 1-256 | Alla tecken. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Namnområden | Globala | 6-50 | Alfanumeriska och bindestreck.<br><br>Börja med en bokstav. Avsluta med en bokstav eller ett nummer.<br><br>Mer information finns i [Skapa namnområde](/rest/api/servicebus/create-namespace). |
> | namnområden / AuthorizationRules | namnområde | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med alphnumeric. |
> | namnrymdor / katastrofRecoveryConfigs | Globala | 6-50 | Alfanumeriska och bindestreck.<br><br>Börja med bokstaven. Avsluta med alfanumeriskt. |
> | namnområden / migreringKonfigureringar | namnområde |  | Bör alltid vara **$default**. |
> | namnområden / köer | namnområde | 1-260 | Alfanumeriska, punkter, bindestreck, understreck och snedstreck.<br><br>Börja och sluta med alfanumeriskt. |
> | namnrymd / köer / auktoriseringRules | kö | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med alphnumeric. |
> | namnområden / ämnen | namnområde | 1-260 | Alfanumeriska, punkter, bindestreck, understreck och snedstreck.<br><br>Börja och sluta med alfanumeriskt. |
> | namnrymd / ämnen / authorizationRules | ämne | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med alphnumeric. |
> | namnrymd / ämnen / prenumerationer | ämne | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med alphnumeric. |
> | namnrymd / ämnen / abonnemang / regler | prenumeration | 1-50 | Alfanumeriska, punkter, bindestreck och understreck.<br><br>Börja och sluta med alphnumeric. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kluster | regionen | 4-23 | Gemener, siffror och bindestreck.<br><br>Börja med gemener. Avsluta med gemener eller tal. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | signalR | Globala | 3–63 | Alfanumeriska och bindestreck.<br><br>Börja med bokstaven. Avsluta med bokstav eller nummer.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | managedInstances | Globala | 1–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att starta eller sluta med bindestreck. |
> | Servrar | Globala | 1–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att starta eller sluta med bindestreck. |
> | servrar /administratörer | server |  | Måste `ActiveDirectory`vara . |
> | servrar / databaser | server | 1-128 | Det går inte att använda:<br>`<>*%&:\/?`<br><br>Det går inte att sluta med punkt eller blanksteg. |
> | servrar / databaser / syncGroups | databas | 1-150 | Alfanumeriska, bindestreck och understreck. |
> | servrar / elasticPools | server | 1-128 | Det går inte att använda:<br>`<>*%&:\/?`<br><br>Det går inte att sluta med punkt eller blanksteg. |
> | servrar /redundansGrupper | Globala | 1–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att starta eller sluta med bindestreck. |
> | servrar / firewallRules | server | 1-128 | Det går inte att använda:<br>`<>*%&:;\/?`<br><br>Kan inte sluta med punkt. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | lagringKonton | Globala | 3–24 | Gemener och siffror. |
> | lagringKonton / blobServices | storage account |  | Måste `default`vara . |
> | lagringKonton / blobServices / behållare | storage account | 3–63 | Gemener, siffror och bindestreck.<br><br>Börja med gemener eller tal. Det går inte att använda på varandra följande bindestreck. |
> | lagringKonton / filTjänster | storage account |  | Måste `default`vara . |
> | lagringKonton / filTjänster / resurser | storage account | 3–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att starta eller sluta med bindestreck. Det går inte att använda på varandra följande bindestreck. |
> | lagringKonton / managementPolicies | storage account |  | Måste `default`vara . |
> | blob | container | 1–1024 | Alla URL-tecken, skiftlägeskänsliga |
> | kö | storage account | 3–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att starta eller sluta med bindestreck. Det går inte att använda på varandra följande bindestreck. |
> | tabell | storage account | 3–63 | Alfanumerika.<br><br>Börja med bokstaven. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | storageSyncServices | Resursgrupp | 1-260 | Alfanumeriska, blanksteg, punkter, bindestreck och understreck.<br><br>Det går inte att sluta med punkt eller blanksteg. |
> | storageSyncServices/syncGroups | synkroniseringstjänst för lagring | 1-260 | Alfanumeriska, blanksteg, punkter, bindestreck och understreck.<br><br>Det går inte att sluta med punkt eller blanksteg. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Chefer | Resursgrupp | 2-50 | Alfanumeriska och bindestreck.<br><br>Börja med bokstaven. Avsluta med alfanumeriskt. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | streamingjobb | Resursgrupp | 3–63 | Alfanumeriska, bindestreck och understreck. |
> | streamingjobb / funktioner | streaming jobb | 3–63 | Alfanumeriska, bindestreck och understreck. |
> | streamingjobb / ingångar | streaming jobb | 3–63 | Alfanumeriska, bindestreck och understreck. |
> | streamingjobb / utgångar | streaming jobb | 3–63 | Alfanumeriska, bindestreck och understreck. |
> | streamingjobb / omvandlingar | streaming jobb | 3–63 | Alfanumeriska, bindestreck och understreck. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Miljöer | Resursgrupp | 1–90 | Det går inte att använda:<br>`'<>%&:\?/#` |
> | miljöer / accessPolicies | miljö | 1–90 | Det går inte att använda:<br> `'<>%&:\?/#` |
> | miljöer / eventSources | miljö | 1–90 | Det går inte att använda:<br>`'<>%&:\?/#` |
> | miljöer / referensDataSets | miljö | 3–63 | Alfanumerika |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | serverfarms | Resursgrupp | 1-40 | Alfanumeriska och bindestreck. |
> | webbplatser | Globala | 2-60 | Innehåller alfanumeriska och bindestreck.<br><br>Det går inte att starta eller sluta med bindestreck. |
> | platser / platser | webbplats | 2-59 | Alfanumeriska och bindestreck. |

## <a name="next-steps"></a>Nästa steg

Rekommendationer om hur du namnger resurser finns i [Klar: Rekommenderad namngivning och taggning konventioner](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
