# n8n Freelancer Starter : guide d'installation (FR)

Deux workflows n8n gratuits pour les freelances qui facturent des clients pros en France, dans l'UE ou au Royaume-Uni. Testés sur n8n 2.22.6 auto-hébergé.

| # | Workflow | Ce qu'il fait | Identifiants |
|---|---|---|---|
| 01 | Vérification TVA et entreprise client | Vérifie un numéro de TVA intracommunautaire sur VIES (service officiel de l'UE) et renvoie la raison sociale, l'adresse, un bloc à coller sur la facture et une indication sur l'autoliquidation | Aucun (API publique gratuite) |
| 03 | Suivi du CA et des seuils | Additionne le CA encaissé de l'année, en plusieurs devises (taux de change gratuits basés sur la BCE), le compare aux seuils (franchise de TVA 37 500 / 41 250 €, plafond micro 83 600 €) et estime les cotisations URSSAF du dernier mois | Aucun |

## Installation

1. n8n Cloud ou auto-hébergé (`npx n8n` avec Node.js 22+, ou Docker).
2. Dans n8n : Workflows, puis Import from File, pour chaque fichier du dossier `workflows/`. Activez ensuite le workflow.
3. Testez avec les commandes `curl` du guide anglais et les fichiers du dossier `tests/`.

## Fonctionnement

- WF01 refuse un numéro mal formé avant d'appeler VIES (statut `INVALID_FORMAT`).
- Si VIES est indisponible pour un pays, WF01 renvoie `VIES_UNAVAILABLE` au lieu de dire que le numéro est invalide. Il suffit de réessayer plus tard.

## Réglages

- WF03 : nœud Code "Compute YTD & alerts", bloc `PRESETS` en haut du code. Préréglages : `FR_MICRO_BNC` (25,6 %), `FR_MICRO_BIC_SERVICES` (21,2 %), `UK_SOLE_TRADER`, `CUSTOM`. Taux et seuils 2026 vérifiés sur autoentrepreneur.urssaf.fr et economie.gouv.fr. Choisissez le préréglage avec `"preset"` dans la requête.

Un kit complet avec relances d'impayés arrive bientôt sur Gumroad.

## Avertissement

Ce sont des estimations : vérifiez les taux et seuils chaque année. Ce n'est pas un conseil juridique ou fiscal. La déclaration URSSAF reste à faire vous-même sur autoentrepreneur.urssaf.fr.
