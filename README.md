# Questionnaire satisfaction livraison — Bocklip

Page questionnaire (GitHub Pages) + backend Google Apps Script + Google Sheet.

```
SMS (ton fournisseur) ──► avis.bocklip.com/?t=TOKEN ──► index.html
                                                          │  GET  ?action=check  (lien valide ?)
                                                          │  POST {token, note, ponctualite, commentaire}
                                                          ▼
                                              Apps Script (Code.gs) ──► Google Sheet
                                                                         ├─ Envois    (1 ligne / livraison)
                                                                         └─ Réponses  (1 ligne / réponse)
```

## 1. Google Sheet + Apps Script

1. Crée une Google Sheet « Satisfaction livraison ».
2. Extensions → Apps Script → colle `Code.gs`.
3. Renseigne `CONFIG` : `EMAIL_ALERTE`, `URL_AVIS_GOOGLE`, `URL_PAGE`.
4. Exécute `setup()` une fois (autorise les accès) → crée les onglets **Envois** et **Réponses**.
5. Déployer → Nouveau déploiement → **Application Web**
   - Exécuter en tant que : **Moi**
   - Accès : **Tout le monde**
6. Copie l'URL `…/exec`.

> Après chaque modification de `Code.gs` : Déployer → Gérer les déploiements → ✏️ → Version : *Nouvelle version*.
> (Sinon l'URL `/exec` continue de servir l'ancienne version.)

## 2. Page sur GitHub Pages

1. Dans `index.html`, remplace `API_URL` par l'URL `/exec`.
2. Ajuste la couleur `--accent` à la charte Bocklip.
3. Crée un repo (ex. `H5pollet/avis-livraison`), pousse `index.html` :
   ```bash
   git init
   git add index.html README.md
   git commit -m "Questionnaire satisfaction livraison"
   git branch -M main
   git remote add origin https://github.com/H5pollet/avis-livraison.git
   git push -u origin main
   ```
4. Repo → Settings → Pages → Source : *Deploy from a branch*, `main` / `root`.
5. Domaine perso : Settings → Pages → Custom domain = `avis.bocklip.com`,
   puis chez ton registrar DNS : `CNAME avis → h5pollet.github.io`. Coche *Enforce HTTPS*.

> Ne mets **pas** `Code.gs` dans un repo public si tu y laisses des emails/IDs internes.

## 3. Utilisation au quotidien

1. Ajoute les livraisons dans **Envois** (N° commande, Client, Téléphone, Date livraison) — à la main, par import ou par formule/script depuis l'onglet Livraisons.
2. Menu **Questionnaire livraison → Générer les liens manquants** → la colonne **Lien** se remplit.
3. Exporte / transmets les liens à ton fournisseur SMS.
4. Les réponses arrivent dans **Réponses** ; la colonne *Répondu* passe à « Oui » dans **Envois**.
5. Note ≤ 4 → email d'alerte. Note ≥ 8 → la page propose un avis Google.

Pour automatiser l'étape 2 : déclencheur horaire sur `genererLiens`.

## Test

- `https://avis.bocklip.com/?t=<un token de l'onglet Envois>` → formulaire
- Re-ouvrir le même lien après réponse → « Vous avez déjà répondu »
- `https://avis.bocklip.com/?t=faux1234` → « Lien invalide »
