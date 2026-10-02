# TP DevOps Correction Docker

1. Les testcontainers permettent de créer des vrais conteneurs docker et qui les détruisent automatiquement à la fin des tests
2. Pour plus de sécurité car les répos github peuvent être vus, clonés, etc... Les variables sécurisées elles, sont chiffrées.
3. Parce que sans cela les 2 jobs démarrent en parallèle et les images docker seraient alors construites même si les tests échouent. Avec needs on attend de voir si les tests sont validés et si ils le sont alors on crés et publie les images.
4. Parce que c'est plus facile à récupérer sans tout recompiler ou avoir le code. De plus ca permet que tout le monde utilise la même image et ca permet de rolleback si jamais y a un problème.
