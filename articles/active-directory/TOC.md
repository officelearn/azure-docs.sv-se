# Översikt
## [Vad är Azure Active Directory?](active-directory-whatis.md)
## [Om Azures identitetshantering](identity-fundamentals.md)
## [Förstå Azure-identitetslösningar](understand-azure-identity-solutions.md)
## [Välj en hybrididentitetslösning](choose-hybrid-identity-solution.md)
## [Associera Azure-prenumerationer](active-directory-how-subscriptions-associated-directory.md)
## [Vanliga frågor och svar](active-directory-faq.md)
## [Nyheter](whats-new.md)


# Kom igång
## [Kom igång med Azure AD](get-started-azure-ad.md)
## [Registrera dig för Azure AD Premium](active-directory-get-started-premium.md)
## [Lägga till ett anpassat domännamn](add-custom-domain.md)
## [Konfigurera varumärkesexponering](customize-branding.md)
## [Lägga till användare i Azure AD](add-users-azure-active-directory.md)
## [Tilldela licenser till användare](license-users-groups.md)
## [Konfigurera lösenordsåterställning via självbetjäning](active-directory-passwords-getting-started.md)


# Gör så här för att
## Planera och designa
### [Förstå Azure AD-arkitektur](active-directory-architecture.md)
### [Anspråksmappning i Azure Active Directory](active-directory-claims-mapping.md)
### [Distribuera en hybrididentitetslösning](active-directory-hybrid-identity-design-considerations-overview.md)
#### Fastställa krav
##### [Identitet](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Katalogsynkronisering](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Flerfaktorautentisering](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Strategi för identitetslivscykel](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planera för datasäkerhet](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Dataskydd](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Innehållshantering](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Åtkomstkontroll](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Incidenthantering](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planera din identitetslivscykel
##### [Uppgifter](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Införandestrategi](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Nästa steg](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Jämförelse av verktyg](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Hantera användare
### [Lägga till nya användare i Azure AD](add-users-azure-active-directory.md)
### [Hantera användarprofiler](active-directory-users-profile-azure-portal.md)
### [Dela konton](active-directory-sharing-accounts.md)
### [Tilldela användare administrativa roller](active-directory-users-assign-role-azure-portal.md)
### [Lägga till gästanvändare från annan katalog (B2B)](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [Administratörer som lägger till B2B-användare](active-directory-b2b-admin-add-users.md)
#### [Informationsarbetare som lägger till B2B-användare](active-directory-b2b-iw-add-users.md)
#### [API och anpassning](active-directory-b2b-api.md)
#### [Kod och Azure PowerShell-exempel](active-directory-b2b-code-samples.md)
#### [Exempel på registreringsportal för självbetjäning](active-directory-b2b-self-service-portal.md)
#### [E-postinbjudan](active-directory-b2b-invitation-email.md)
#### [Inlösning av inbjudan](active-directory-b2b-redemption-experience.md)
#### [Lägga till B2B-användare utan inbjudan](active-directory-b2b-add-user-without-invite.md)
#### [Villkorlig åtkomst för B2B](active-directory-b2b-mfa-instructions.md)
#### [B2B-delningsprinciper](active-directory-b2b-delegate-invitations.md)
#### [Lägg till en B2B-användare till en roll](active-directory-b2b-add-guest-to-role.md)
#### [Dynamiska grupper och B2B-användare](active-directory-b2b-dynamic-groups.md)
#### [Granskning och rapporter](active-directory-b2b-auditing-and-reporting.md)
#### [Extern delning av B2B och Office 365](active-directory-b2b-o365-external-user.md)
#### [B2B-licensiering](active-directory-b2b-licensing.md)
#### [Aktuella begränsningar](active-directory-b2b-current-limitations.md)
#### [Vanliga frågor och svar](active-directory-b2b-faq.md)
#### [Felsökning av B2B](active-directory-b2b-troubleshooting.md)
#### [Förstå B2B-användaren](active-directory-b2b-user-properties.md)
#### [Användartoken för B2B](active-directory-b2b-user-token.md)
#### [B2B för Azure AD-integrerade appar](active-directory-b2b-configure-saas-apps.md)
#### [Mappning av användaranspråk för B2B](active-directory-b2b-claims-mapping.md)
#### [Jämför B2B-samarbete med B2C](active-directory-b2b-compare-b2c.md)
#### [Få support för B2B](active-directory-b2b-support.md)

## [Hantera grupper och medlemmar](active-directory-manage-groups.md)
### Hantera grupper
#### [Azure-portal](active-directory-groups-create-azure-portal.md)
#### [Klassisk portal](active-directory-accessmanagement-manage-groups.md)
#### [Azure PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Hantera gruppmedlemmar](active-directory-groups-members-azure-portal.md)
### [Hantera gruppägare](active-directory-accessmanagement-managing-group-owners.md)
### [Hantera gruppmedlemskap](active-directory-groups-membership-azure-portal.md)
### [Tilldela licenser med hjälp av grupper](active-directory-licensing-whatis-azure-portal.md)
#### [Tilldela licenser till en grupp](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identifiera och åtgärda licensproblem i en grupp](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrera enskilda licensierade användare till gruppbaserad licensiering](active-directory-licensing-group-migration-azure-portal.md)
#### [Fler scenarier för gruppbaserad licensiering](active-directory-licensing-group-advanced.md)
#### [Azure PowerShell-exempel för gruppbaserad licensiering](active-directory-licensing-ps-examples.md)
#### [Referens för produkter och tjänstplaner i Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Konfigurera förfallodatum för Office 365-grupper](active-directory-groups-lifecycle-azure-portal.md)
### [Visa alla grupper](active-directory-groups-view-azure-portal.md)
### [Hantera gruppåtkomst till SaaS-appar](active-directory-accessmanagement-group-saasapps.md)
### [Återställa en borttagen Office 365-grupp](active-directory-groups-restore-azure-portal.md)
### Hantera gruppinställningar
#### [Azure-portal](active-directory-groups-settings-azure-portal.md)
#### [Cmdletar](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Skapa avancerade regler
#### [Azure-portal](active-directory-groups-dynamic-membership-azure-portal.md)
#### [Klassisk portal](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [Konfigurera självbetjäningsgrupper](active-directory-accessmanagement-self-service-group-management.md)
### [Felsöka](active-directory-accessmanagement-troubleshooting.md)

## [Hantera rapporter](active-directory-reporting-azure-portal.md)
### [Inloggningsaktiviteter](active-directory-reporting-activity-sign-ins.md)
### [Granska aktivitet](active-directory-reporting-activity-audit-logs.md)
### [Användare i riskzonen](active-directory-reporting-security-user-at-risk.md)
### [Riskfyllda inloggningar](active-directory-reporting-security-risky-sign-ins.md)
### [Riskhändelser](active-directory-reporting-risk-events.md)
### [Vanliga frågor och svar](active-directory-reporting-faq.md)
### Uppgifter
#### [Konfigurera namngivna platser](active-directory-named-locations.md)
#### [Hitta aktivitetsrapporter](active-directory-reporting-migration.md)
#### [Använd innehållspaketet för Azure Active Directory Power BI](active-directory-reporting-power-bi-content-pack-how-to.md)
### Referens
#### [Kvarhållning](active-directory-reporting-retention.md)
#### [Svarstider](active-directory-reporting-latencies-azure-portal.md)
#### [Meddelanden](active-directory-reporting-notifications.md)
#### [Felkoder för inloggningsaktivitet](active-directory-reporting-activity-sign-ins-errors.md)
#### [Multi-Factor Authentication](active-directory-reporting-activity-sign-ins-mfa.md)
### Felsöka
#### [Granskningsdata som saknas](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Data som saknas i nedladdningar](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Fel i innehållspaket för Azure Active Directory-aktivitetsloggar](active-directory-reporting-troubleshoot-content-pack.md)
### [Programmässig åtkomst](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Granska referens](active-directory-reporting-api-audit-reference.md)
#### [Inloggningsreferens](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Förutsättningar](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Granska exempel](active-directory-reporting-api-audit-samples.md)
#### [Inloggningsexempel](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Använda certifikat](active-directory-reporting-api-with-certificates.md)

## Hantera lösenord
### [Lösenordsöversikt](active-directory-passwords-overview.md)
### Användardokument
#### [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md)
#### [Metodtips för lösenord](active-directory-secure-passwords.md)
#### [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md)
### [SSPR – så här fungerar det](active-directory-passwords-how-it-works.md)
### [SSPR-distributionsguide](active-directory-passwords-best-practices.md)
### [SSPR-principer ](active-directory-passwords-policy.md)
### [SSPR-anpassning](active-directory-passwords-customize.md)
### [SSPR-datakrav](active-directory-passwords-data.md)
### [SSPR-rapportering](active-directory-passwords-reporting.md)
### IT-administratörer: Återställ lösenord
#### [Azure Portal](active-directory-users-reset-password-azure-portal.md)
### [Licensiera SSPR](active-directory-passwords-licensing.md)
### [Tillbakaskrivning av lösenord](active-directory-passwords-writeback.md)
### [Felsöka](active-directory-passwords-troubleshoot.md)
### [Vanliga frågor och svar](active-directory-passwords-faq.md)


## Hantera enheter
### [Introduktion](device-management-introduction.md)
### [Använd Azure Portal](device-management-azure-portal.md)
### [Planera Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Vanliga frågor och svar](device-management-faq.md)
### Uppgifter
#### [Konfigurera Azure AD-registrerade Windows 10-enheter](device-management-azuread-registered-devices-windows10-setup.md)
#### [Konfigurera Azure AD-anslutna enheter](device-management-azuread-joined-devices-setup.md)
#### [Konfigurera Azure AD-anslutna hybrid-enheter](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Distribuera lokalt](active-directory-device-registration-on-premises-setup.md)
#### [Azure AD-anslutning under första körningen av Windows 10](device-management-azuread-joined-devices-frx.md)
### Felsöka
#### [Azure AD-anslutna Windows 10- och Windows Server 2016-hybridenheter](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Tidigare Azure AD-anslutna Windows-hybridenheter ](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Hantera appar
### [Översikt](active-directory-enable-sso-scenario.md)
### [Komma igång](active-directory-integrating-applications-getting-started.md)
### [Självstudier om SaaS-appintegration](active-directory-saas-tutorial-list.md)
### [Cloud App Discovery](cloudappdiscovery-get-started.md)
#### [Skapa ögonblicksrapporter](cloudappdiscovery-set-up-snapshots.md)
#### [Konfigurera kontinuerlig rapportering](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Använda en parser för loggar](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
#### Agentbaserad identifiering
##### [Vad är Cloud App Discovery?](active-directory-cloudappdiscovery-whatis.md)
##### [Uppdatera registerinställningar](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
##### [Förstå säkerhet och sekretess](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)


### [Få åtkomst till appar med App Proxy](active-directory-application-proxy-get-started.md)
#### Kom igång
##### [Aktivera App Proxy](active-directory-application-proxy-enable.md)
##### [Publicera appar](application-proxy-publish-azure-portal.md)
##### [Anpassade domäner](active-directory-application-proxy-custom-domains.md)
#### [Enkel inloggning](application-proxy-sso-overview.md)
##### [Enkel inloggning med KCD](active-directory-application-proxy-sso-using-kcd.md)
##### [Enkel inloggning med rubriker](application-proxy-ping-access.md)
##### [Enkel inloggning med lösenordsvalv](application-proxy-sso-azure-portal.md)
#### Koncept
##### [Anslutningsappar](application-proxy-understand-connectors.md)
##### [Säkerhet](application-proxy-security-considerations.md)
##### [Nätverk](application-proxy-network-topology-considerations.md)


##### [Uppgradera från TMG eller UAG](application-proxy-transition-from-uag-tmg.md)

#### Avancerade konfigurationer
##### [Publicera i separata nätverk](active-directory-application-proxy-connectors-azure-portal.md)
##### [Proxyservrar](application-proxy-working-with-proxy-servers.md)
##### [Anspråksmedvetna appar](active-directory-application-proxy-claims-aware-apps.md)
##### [Ursprungliga klientappar](active-directory-application-proxy-native-client.md)
##### [Tyst installation](active-directory-application-proxy-silent-installation.md)
##### [Anpassad startsida](application-proxy-office365-app-launcher.md)
##### [Översätt infogade länkar](application-proxy-link-translation.md)
#### Publicera genomgångar
##### [Fjärrskrivbord](application-proxy-publish-remote-desktop.md)
##### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
##### [Microsoft Teams](application-proxy-teams.md)
#### [Felsöka](active-directory-application-proxy-troubleshoot.md)
#### Använd den klassiska portalen
##### [Ladda ned anslutningsappar](application-proxy-enable-classic-portal.md)
##### [Publicera appar](active-directory-application-proxy-publish.md)
##### [Använd anslutningsappar](active-directory-application-proxy-connectors.md)
##### [Villkorlig åtkomst](active-directory-application-proxy-conditional-access.md)

### Hantera företagets appar
#### [Tilldela användare](active-directory-coreapps-assign-user-azure-portal.md)
#### [Anpassa profilering](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [Inaktivera användarinloggningar](active-directory-coreapps-disable-app-azure-portal.md)
#### [Ta bort användare](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Visa alla mina appar](active-directory-coreapps-view-azure-portal.md)
#### [Hantera användarens kontoetablering](active-directory-enterprise-apps-manage-provisioning.md)
#### [Hantera enkel inloggning för företagsappar](active-directory-enterprise-apps-manage-sso.md)
#### [Avancerad certifikatsignering för SAML-appar](active-directory-enterprise-apps-advance-certificate-options.md)
#### [Dölja en tredjepartsapp för en användare](active-directory-coreapps-hide-third-party-app.md)

### [Hantera åtkomst till appar](active-directory-managing-access-to-apps.md)
#### [Åtkomst med självbetjäning](active-directory-self-service-application-access.md)
#### [Åtkomst med enkel inloggning](active-directory-appssoaccess-whatis.md)
#### [Certifikat för enkel inloggning](active-directory-sso-certs.md)
#### [Klientrestriktioner](active-directory-tenant-restrictions.md)
#### [Använd SCIM-etablering av användare](active-directory-scim-provisioning.md)

### [Felsöka](active-directory-application-troubleshoot-content-map.md)
#### [Programutveckling](active-directory-application-dev-troubleshoot-content-map.md)
##### [Konfiguration och registrering](active-directory-application-dev-config-content-map.md)
##### [Utveckling](active-directory-application-dev-development-content-map.md)
#### [Programhantering](active-directory-application-management-troubleshoot-content-map.md)
##### [Konfiguration](active-directory-application-config-content-map.md)
##### [Inloggning](active-directory-application-sign-in-content-map.md)
##### [Etablering](active-directory-application-provisioning-content-map.md)
##### [Hantera åtkomst](active-directory-application-access-content-map.md)
##### [Åtkomstpanel](active-directory-application-access-panel-content-map.md)
##### [Programproxy](active-directory-application-proxy-content-map.md)
##### [Villkorlig åtkomst](active-directory-application-conditional-access-content-map.md)
### [Utveckla appar](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Dokumentbibliotek](active-directory-apps-index.md)

## Hantera din katalog
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Egna domännamn
#### [Översikt](active-directory-add-domain-concepts.md)
#### [Hantera domännamn](active-directory-domains-manage-azure-portal.md)
##### [Klassisk portal](active-directory-add-manage-domain-names.md)
### [Administrera din katalog](active-directory-administer.md)
### [Flera kataloger](active-directory-licensing-directory-independence.md)
### [Registrering av självbetjäning](active-directory-self-service-signup.md)
#### [Ta över en ohanterad katalog](domains-admin-takeover.md)
### [Företagsroaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Aktivera](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Grupprincipinställningar](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10-inställningar](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Vanliga frågor och svar](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Felsöka](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Integrera lokala identiteter med Azure AD Connect](./connect/active-directory-aadconnect.md)

## [Hantera åtkomst till Azure](toc.yml)

## Delegera åtkomst till resurser
### [Administratörsroller](active-directory-assign-admin-roles-azure-portal.md)
#### [Tilldela administratörsroller](active-directory-users-assign-role-azure-portal.md)
### [Administrativa enheter](active-directory-administrative-units-management.md)
### [Konfigurera livslängd för token](active-directory-configurable-token-lifetimes.md)

## Åtkomstgranskningar
### [Översikt över åtkomstgranskningar](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Slutför en åtkomstgranskning](active-directory-azure-ad-controls-complete-access-review.md)
### [Skapa en åtkomstgranskning](active-directory-azure-ad-controls-create-access-review.md)
### [Så här utför du en åtkomstgranskning](active-directory-azure-ad-controls-perform-access-review.md)
### [Så här granskar du din åtkomst](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Gäståtkomst med åtkomstgranskningar](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Hantera användaråtkomst med granskningar](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Hantera program och kontroller](active-directory-azure-ad-controls-manage-programs-controls.md)


## Skydda dina identiteter
### [Villkorlig åtkomst](active-directory-conditional-access-azure-portal.md)
#### [Kontroller](active-directory-conditional-access-controls.md)
#### [Komma igång](active-directory-conditional-access-azure-portal-get-started.md)
#### [Bästa praxis](active-directory-conditional-access-best-practices.md)
#### [Förstå enhetsprinciper för Office 365-tjänster](active-directory-conditional-access-device-policies.md)
#### Uppgifter
##### [Migrera klassiska principer](active-directory-conditional-access-migration.md)
##### [Konfigurera enhetsbaserad villkorlig åtkomst](active-directory-conditional-access-policy-connected-applications.md)
##### [Konfigurera appbaserad villkorlig åtkomst](active-directory-conditional-access-mam.md)
##### [Ange användningsvillkor för användare och appar](active-directory-tou.md)
##### [Konfigurera VPN-anslutning](active-directory-conditional-access-vpn-connectivity-windows10.md)
##### [Konfigurera SharePoint och Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Reparation](active-directory-conditional-access-device-remediation.md)
#### [Teknisk referens](active-directory-conditional-access-technical-reference.md)
#### [Vanliga frågor och svar](active-directory-conditional-faqs.md)

### Windows Hello
#### [Autentisera utan lösenord](active-directory-azureadjoin-passport.md)
#### [Aktivera Windows Hello för företag](active-directory-azureadjoin-passport-deployment.md)
### Certifikatbaserad autentisering
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Kom igång](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Aktivera](active-directory-identityprotection-enable.md)
#### [Hitta sårbarheter](active-directory-identityprotection-vulnerabilities.md)
#### [Riskhändelser](active-directory-identity-protection-risk-events.md)
#### [Meddelanden](active-directory-identityprotection-notifications.md)
#### [Inloggning](active-directory-identityprotection-flows.md)
#### [Simulera riskhändelser](active-directory-identityprotection-playbook.md)
#### [Avblockera användare](active-directory-identityprotection-unblock-howto.md)
#### [Vanliga frågor och svar](active-directory-identity-protection-faqs.md)
#### [Ordlista](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [Integrera andra tjänster med Azure AD]()
### [Aktivera LinkedIn-integrering](linkedin-integration.md)

## [Distribuera AD DS på virtuella Azure-datorer](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory på virtuella Azure-datorer](active-directory-deploying-ws-ad-guidelines.md)
### [Replikeringsdomänkontrollant i ett virtuellt Azure-nätverk](active-directory-install-replica-active-directory-domain-controller.md)
### [Ny skog i ett virtuellt Azure-nätverk](active-directory-new-forest-virtual-machine.md)



## [Distribuera AD FS i Azure](active-directory-aadconnect-azure-adfs.md)
### [Hög tillgänglighet](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Ändra signaturens hashalgoritm](active-directory-federation-sha256-guidance.md)

## [Felsöka](active-directory-troubleshooting-support-howto.md)
### [Felsöka Active Directory-objekt som saknas eller inte är tillgängligt](active-directory-troubleshooting.md)

## Distribuera Azure AD PoC (Proof of Concept)
### [PoC-strategibok: Introduktion](active-directory-playbook-intro.md)
### [PoC-strategibok: Ingredienser](active-directory-playbook-ingredients.md)
### [PoC-strategibok: Implementering](active-directory-playbook-implementation.md)
### [PoC-strategibok: Komponenter](active-directory-playbook-building-blocks.md)


# Referens
## [Kodexempel](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [Azure PowerShell-cmdletar](/powershell/azure/overview)
## [Java API-referens](/java/api)
## [.NET-API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Tjänstens begränsningar](active-directory-service-limits-restrictions.md)

# Relaterat
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD för utvecklare](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Resurser
## [Azures feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Prissättning](https://azure.microsoft.com/pricing/details/active-directory/)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
