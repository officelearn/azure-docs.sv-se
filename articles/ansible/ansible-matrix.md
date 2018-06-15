---
title: Ansible modul och version matris för Azure
description: Ansible modul och version matris för Azure
ms.service: ansible
keywords: ansible, roles, matrix, version, azure, devops
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 03/25/2018
ms.topic: article
ms.openlocfilehash: 011cb173ffdecc7a22c2e470209719ccaf6bda58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "30834154"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible modul och version matris

## <a name="ansible-modules-for-azure"></a>Ansible moduler för Azure
Ansible levereras med ett antal moduler som kan utföras direkt på fjärrvärdar eller via playbooks.
Den här artikeln innehåller Ansible-moduler för Azure som kan etablera Azure-molnresurser, t.ex virtuella datorer, nätverk och behållartjänster. Du kan hämta dessa moduler från den officiella versionen av Ansible eller från följande playbook roller som publicerats av Microsoft.

| Ansible-modulen för Azure                   |  Ansible 2.4 |  Ansible 2,5 |  Playbook roll [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|-----------------------------|-------------------------------------| 
| **Compute**                    |           |                          |                                  | 
| azure_rm_availabilityset                    | Ja          | Ja                         | Ja                                 | 
| azure_rm_availabilityset_facts              | Ja          | Ja                         | Ja                                 | 
| azure_rm_deployment                         | Ja          | Ja                         | Ja                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Ja          | Ja                         | Ja                                 | 
| azure_rm_virtualmachineimage_facts          | Ja          | Ja                         | Ja                                 | 
| azure_rm_resourcegroup                      | Ja          | Ja                         | Ja                                 | 
| azure_rm_resourcegroup_facts                | Ja          | Ja                         | Ja                                 | 
| azure_rm_virtualmachine                     | Ja          | Ja                         | Ja                                 | 
| azure_rm_virtualmachine_extension           | Ja          | Ja                         | Ja                                 | 
| azure_rm_virtualmachine_scaleset            | Ja          | Ja                         | Ja                                 | 
| azure_rm_image                              |              | Ja                         | Ja                                 | 
| **Nätverk**                    |           |                          |                                  | 
| azure_rm_virtualnetwork                     | Ja          | Ja                         | Ja                                 | 
| azure_rm_virtualnetwork_facts               | Ja          | Ja                         | Ja                                 | 
| azure_rm_subnet                             | Ja          | Ja                         | Ja                                 | 
| azure_rm_networkinterface                   | Ja          | Ja                         | Ja                                 | 
| azure_rm_networkinterface_facts             | Ja          | Ja                         | Ja                                 | 
| azure_rm_publicipaddress                    | Ja          | Ja                         | Ja                                 | 
| azure_rm_publicipaddress_facts              | Ja          | Ja                         | Ja                                 | 
| azure_rm_dnsrecordset                       | Ja          | Ja                         | Ja                                 | 
| azure_rm_dnsrecordset_facts                 | Ja          | Ja                         | Ja                                 | 
| azure_rm_dnszone                            | Ja          | Ja                         | Ja                                 | 
| azure_rm_dnszone_facts                      | Ja          | Ja                         | Ja                                 | 
| azure_rm_loadbalancer                       | Ja          | Ja                         | Ja                                 | 
| azure_rm_loadbalancer_facts                 | Ja          | Ja                         | Ja                                 | 
| azure_rm_appgw                              | -            | -                           | Ja                                 | 
| azure_rm_appgwroute                         | -            | -                           | Ja                                 | 
| azure_rm_appgwroute                         | -            | -                           | Ja                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | Ja                                 |
| azure_rm_appgwroutetable                    | -            | -                           | Ja                                 |
| azure_rm_securitygroup                      | Ja          | Ja                         | Ja                                 | 
| azure_rm_appgwroutetable_facts              | -            | -                           | Ja                                 | 
| **Storage**                    |           |                          |                                  | 
| azure_rm_storageaccount                     | Ja          | Ja                         | Ja                                 | 
| azure_rm_storageaccount_facts               | Ja          | Ja                         | Ja                                 | 
| azure_rm_storageblob                        | Ja          | Ja                         | Ja                                 | 
| azure_rm_managed_disk                       | Ja          | Ja                         | Ja                                 | 
| azure_rm_managed_disk_facts                 | Ja          | Ja                         | Ja                                 | 
| **Behållare**                    |           |                          |                                  | 
| azure_rm_acs                                | Ja          | Ja                         | Ja                                 | 
| azure_rm_containerinstance                  | -            | Ja                         | Ja                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | Ja                                 | 
| azure_rm_containerregistry                  | -            | Ja                         | Ja                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | Ja                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | Ja                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | Ja                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | Ja                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | Ja                                 | 
| **Azure Functions**                    |           |                          |                                  | 
| azure_rm_functionapp                        | Ja          | Ja                         | Ja                                 | 
| azure_rm_functionapp_facts                  | Ja          | Ja                         | Ja                                 | 
| **Databaser**                    |           |                          |                                  | 
| azure_rm_sqlserver                          | -            | Ja                         | Ja                                 | 
| azure_rm_sqlserver_facts                    | -            | Ja                         | Ja                                 | 
| azure_rm_sqldatabase                        | -            | Ja                         | Ja                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | Ja                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | Ja                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | Ja                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | Ja                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | Ja                                 | 
| azure_rm_mysqlserver                        | -            | Ja                         | Ja                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | Ja                                 | 
| azure_rm_mysqldatabase                      | -            | Ja                         | Ja                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | Ja                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | Ja                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | Ja                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | Ja                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | Ja                                 | 
| azure_rm_postgresqlserver                   | -            | Ja                         | Ja                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | Ja                                 | 
| azure_rm_postgresqldatabase                 | -            | Ja                         | Ja                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | Ja                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | Ja                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | Ja                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | Ja                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | Ja                                 | 
| **Key Vault**                    |           |                          |                                  | 
| azure_rm_keyvault                           | -            | Ja                         | Ja                                 |
| azure_rm_keyvault_facts                     | -            | -                           | Ja                                 |
| azure_rm_keyvaultkey                        | -            | Ja                         | Ja                                 |
| azure_rm_keyvaultsecret                     | -            | Ja                         | Ja                                 |


## <a name="introduction-to-playbook-role-for-azure"></a>Introduktion till playbook roll för Azure
Den [azure_preview_module playbook rollen](https://galaxy.ansible.com/Azure/azure_preview_modules/) är den mest omfattande rollen och innehåller alla de senaste Azure modulerna. Uppdateringar och felkorrigeringar görs i mer tid än den officiella Ansible-versionen. Om du använder Ansible för Azure-resurs etablering syfte är du uppmanas att installera rollen azure_preview_module.

Rollen azure_preview_module playbook släpps alla tre veckor.

## <a name="next-steps"></a>Nästa steg
Mer information som rör playbook roller, referera till [skapa återanvändningsbara Playbooks](http://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
