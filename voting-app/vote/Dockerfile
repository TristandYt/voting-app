# ----------------------------------------------------
# Étape 1 : Builder 
FROM python:3.13-slim AS builder

# Définit le répertoire de travail dans le conteneur.
WORKDIR /app/vote

# Copie le fichier de dépendances
COPY requirements.txt .

# Installe les dépendances. --no-cache-dir pour une image plus légère.
RUN pip install --no-cache-dir -r requirements.txt

# ----------------------------------------------------
# Étape 2 : Image Finale (Image d'exécution)

FROM python:3.13-slim

# Définit le répertoire de travail final.
WORKDIR /app/vote

# Copie les packages Python installés depuis l'étape 'builder'
COPY --from=builder /usr/local/lib/python3.13/site-packages /usr/local/lib/python3.13/site-packages

# Copie le code source de l'application (app.py, etc.)
COPY . .

# Déclare le port d'écoute de l'application (5000 par défaut pour Flask)
EXPOSE 5000


CMD ["python", "app.py"]