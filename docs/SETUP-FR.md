# Kit « Se faire payer » pour freelances — Guide d'installation (FR)

Trois workflows n8n prêts à importer (testés sur n8n 2.22.6 auto-hébergé) pour freelances et petites agences qui facturent des clients pros, en France et dans l'UE.

| # | Workflow | Ce qu'il fait | Identifiants nécessaires |
|---|---|---|---|
| 01 | Vérification TVA & entreprise client | Vérifie un n° de TVA intracommunautaire sur VIES (service officiel UE), renvoie raison sociale + adresse, un bloc prêt à coller sur la facture et une indication d'autoliquidation | **Aucun** (API publique gratuite) |
| 02 | Relances impayés avec pénalités légales | Classe les factures (rappel → relance amicale → ferme → mise en demeure), calcule pénalités de retard + indemnité forfaitaire de 40 €, rédige l'e-mail en FR ou EN | **Aucun**. Optionnel : SMTP/Gmail pour envoyer |
| 03 | Suivi du CA et des seuils | Additionne le CA encaissé de l'année en plusieurs devises (taux de change gratuits basés BCE), compare aux seuils (franchise TVA 37 500 / 41 250 €, plafond micro 83 600 €), estime les cotisations URSSAF du dernier mois | **Aucun** |

## Installation
1. n8n Cloud ou auto-hébergé (`npx n8n` avec Node.js 22+, ou Docker).
2. **Workflows → Import from File** pour chaque fichier de `workflows/`, puis activer.
3. Tester avec les commandes `curl` du guide anglais et les fichiers de `tests/`.

## Réglages
- **WF02** (nœud *Compute reminders*, bloc `CONFIG`) : taux BCE (`referenceRatePct`, actuellement 2,65 % au 16 sept. 2026, à mettre à jour tous les 6 mois), `marginPts` : **10 en France** (taux BCE + 10 points, art. L441-10 du Code de commerce, sauf taux prévu dans vos CGV ; minimum légal : 3 × taux d'intérêt légal), 8 par défaut dans l'UE. Clients particuliers : `"b2b": false` (pas de pénalité ajoutée). Mode test par défaut : rien n'est envoyé.
- **WF03** (nœud *Compute YTD & alerts*, `PRESETS`) : `FR_MICRO_BNC` (25,6 %), `FR_MICRO_BIC_SERVICES` (21,2 %), `UK_SOLE_TRADER`, `CUSTOM`. Taux et seuils 2026 vérifiés sur autoentrepreneur.urssaf.fr et economie.gouv.fr.

## Avertissement
Estimations uniquement : vérifiez les taux et seuils chaque année. Ce n'est pas un conseil juridique ou fiscal. La déclaration URSSAF reste à faire vous-même sur autoentrepreneur.urssaf.fr.
