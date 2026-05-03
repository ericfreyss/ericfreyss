Problème : Icônes Windows 11 remplacées par des carrés
1. Lancer l'éditeur de registre
2. Aller dans \HKEY_CURRENT_USER\Software\Microsoft\Windows NT\CurrentVersion\Fonts
3. Supprimer toutes les références à des versions utilisateur de Segoe Fluent Icons (il se peut que vous ayez tenté d'installer de nouvelles versions, notamment 1.0)
4. Télécharger la dernière version de la fonte: https://aka.ms/SegoeFluentIcons (lien officiel Microsoft)
=> Ce lien est mis à disposition par Microsoft pour installer la fonte sur Windows 10
5. Extraire le fichier Segoe Fluent Icons.ttf
6. Faire un clic droit dessus
7. Choisir "Afficher d'autres options" (tout en bas du menu contextuel normalement)
8. Cliquer sur "Installer pour tous les utilisateurs" et valider l'accès administrateur
9. L'interface demande à passer outre le fait qu'une police est déjà installée, cliquer sur Oui
