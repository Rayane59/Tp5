# TP05 - Creer et deployer un modele avec une plateforme AutoML

Le modele AutoML (FLAML) et le modele manuel du TP2 sont enregistres dans le registre MLflow,
promus avec l'alias `production`, servis en HTTP (ports 8100 et 8101) puis compares.

## Reproduire
    python -m venv .venv ; .venv\Scripts\Activate.ps1 ; pip install -r requirements.txt
    python donnees.py
    mlflow server --host 127.0.0.1 --port 5000 --backend-store-uri sqlite:///mlflow.db --default-artifact-root ./mlartifacts
    python src/automl_deploy.py ; python src/modele_manuel.py
    python src/promouvoir.py fraude-automl ; python src/promouvoir.py fraude-manuel
    mlflow models serve -m "models:/fraude-automl@production" -p 8100 --env-manager local
    mlflow models serve -m "models:/fraude-manuel@production" -p 8101 --env-manager local
    python src/client.py 8100 ; python src/client.py 8101

Resultats et decision : `reports/tp5_comparaison.md`.
