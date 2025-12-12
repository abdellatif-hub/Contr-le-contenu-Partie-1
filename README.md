# README – Portail Universitaire Sécurisé avec Google reCAPTCHA v2
### 🔐 Objectif du Projet

Ce projet consiste à sécuriser une page de connexion universitaire en intégrant Google reCAPTCHA v2 (Checkbox) afin de bloquer les accès automatisés (bots) et protéger les services internes de l’université.

**Ce TP met en œuvre :**

- Un formulaire de connexion HTML

- L’intégration du widget Google reCAPTCHA v2

- Un backend Flask capable de vérifier la validité du reCAPTCHA

- Des tests démontrant la protection contre les soumissions automatisées

--- 
## 📁 Structure du Projet
```
tp_recaptcha/
│
├── app.py            
├── .env                  
│
└── templates/
       └── login.html      
```

--- 

## 🧩 1. Configuration de Google reCAPTCHA

**1.** Se rendre sur : https://www.google.com/recaptcha/admin

**2.** Créer un nouveau site :

- Type : reCAPTCHA v2 – “I’m not a robot” Checkbox

- Domaine : localhost

**3.** Récupérer :

- Site Key

- Secret Key

**4.** Ajouter la Secret Key dans un fichier .env :

```
RECAPTCHA_SECRET_KEY=ta_cle_secrete
```
--- 

# 🧩 2. Code Source de la Page HTML (login.html)
```
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>Portail Universitaire – Connexion</title>
    <script src="https://www.google.com/recaptcha/api.js" async defer></script>
</head>
<body>
    <h2>Connexion au Portail Universitaire</h2>

    <form action="/login" method="POST">
        <input type="text" name="identifiant" placeholder="Identifiant universitaire" required><br><br>

        <input type="password" name="password" placeholder="Mot de passe" required><br><br>

        <div class="g-recaptcha" data-sitekey="VOTRE_SITE_KEY"></div><br>

        <button type="submit">Se connecter</button>
    </form>
</body>
</html>

```

---

# 🧩 3. Backend Flask – Vérification reCAPTCHA (app.py)

```
from flask import Flask, render_template, request
import requests
import os
from dotenv import load_dotenv

load_dotenv()

app = Flask(__name__)

SECRET_KEY = os.getenv("RECAPTCHA_SECRET_KEY")

@app.route("/")
def index():
    return render_template("login.html")

@app.route("/login", methods=["POST"])
def login():
    recaptcha_response = request.form.get("g-recaptcha-response")

    if not recaptcha_response:
        return "Erreur : reCAPTCHA manquant ! Accès refusé."

    verification_url = "https://www.google.com/recaptcha/api/siteverify"
    payload = {
        "secret": SECRET_KEY,
        "response": recaptcha_response
    }

    response = requests.post(verification_url, data=payload)
    result = response.json()

    if result.get("success"):
        return "Connexion autorisée !"
    else:
        return "Échec reCAPTCHA : accès refusé."

if __name__ == "__main__":
    app.run(debug=True)

```

---

# 🧪 4. Tests & Vérification Anti-Bot
## ✔️ 1. Affichage du formulaire avec reCAPTCHA

->📸 Insérer la capture d’écran ici

--- 

## ❌ 2. Tentative de connexion sans reCAPTCHA

->📸 Insérer la capture d’écran ici

---

## ✔️ 3. Connexion valide avec reCAPTCHA coché

->📸 Insérer la capture d’écran ici

---

# 📝 Conclusion

La mise en place du reCAPTCHA v2 dans le portail de connexion universitaire permet d’ajouter une couche de sécurité essentielle contre les attaques automatisées. Grâce à l’intégration du widget côté client et à la vérification côté serveur via Flask, le système distingue efficacement les utilisateurs légitimes des bots, garantissant ainsi une meilleure protection des services internes.
Ce travail pratique démontre la capacité à combiner des mécanismes de sécurité modernes avec un développement web structuré, et constitue une base solide pour la conception d'applications sécurisées dans un environnement professionnel.



