# Rapport d’incident et post‑mortem

## Informations générales

- Date et heure de l’incident : 29 juillet 2025, 14:58 CET
- Services impactés : API TODO
- Gravité : modérée

## Résumé

Une erreur 500 a été déclenchée volontairement via l’endpoint `/error` de l’API Flask. Cet incident simulé a permis de tester l’observabilité de l’application. L'erreur a été rapidement identifiée dans Grafana et confirmée par les métriques exposées via Prometheus. L'impact utilisateur est nul dans le cadre de ce test contrôlé.

## Timeline

| Heure (CET) | Événement |
|-------------|-----------|
| 14:58 | Déclenchement de l’erreur 500 lors de l’appel à `/error` |
| 14:59 | L’erreur est visible dans Grafana dans la métrique `flask_http_request_total{status="500"}` |
| 15:00 | Vérification manuelle via `curl` et dans les logs |
| 15:02 | Confirmation que le monitoring fonctionne comme prévu |

## Détection et diagnostic

L’incident a été détecté dans Grafana via une montée du taux de requêtes HTTP avec le statut 500. Les métriques issues de Prometheus (`flask_http_request_total`, `flask_http_request_duration_seconds`, etc.) ont permis d’identifier rapidement l’endpoint en cause (`/error`) et la nature de l’exception. Aucun comportement inattendu n’a été observé au-delà de ce test.

## Cause racine

L’incident a été déclenché volontairement pour tester la résilience et le monitoring. Il s'agit d'une division par zéro dans la route `/error` de `app.py`. Ce comportement est prévu pour simuler une panne.

Facteurs contributifs :
- Absence de gestion d'erreur personnalisée sur cet endpoint, ce qui est normal ici car l’objectif est de générer une erreur serveur.

## Actions correctives et rétablissement

Aucune action corrective n’a été nécessaire. Le service a continué à fonctionner normalement pour les autres endpoints. Ce test a confirmé que la stack de monitoring était bien en place et opérationnelle.

## Leçons apprises et actions de prévention

- Les métriques Prometheus sont correctement exposées et collectées par Grafana.
- Le tableau de bord Grafana permet de détecter immédiatement les erreurs HTTP.
- La simulation a confirmé que la supervision détecte correctement les erreurs serveur.
- Pour un environnement de production, il serait utile d’ajouter un middleware de gestion d’erreur personnalisée.
- L’ensemble de la chaîne CI/CD et monitoring fonctionne comme prévu.

