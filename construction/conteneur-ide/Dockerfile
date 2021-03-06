# +++++ ANSIBLE templates +++++
# - 
# La directive 'FROM' doit être templatisée avec Ansible, pour mapper les valeurs des 
# variables d'environnement du fichier ["./.env"] suivantes : 
# - 
#    IMAGES_MARGUERITE_COMPANY_NAME=marguerite
#    IMAGE_MARGUERITE_STACK_METEOR_PRODUCT_NAME=meteor-stack
#    # Marguerite vous impose la compatibilité semver
#    VERSION_IMAGE_MARGUERITE_STACK_METEOR=1.0.0
# - 
# Et le templating à effectuer est le suivant : 
# - 
#    FROM $IMAGES_MARGUERITE_COMPANY_NAME/$IMAGE_MARGUERITE_STACK_METEOR_PRODUCT_NAME:$VERSION_IMAGE_MARGUERITE_STACK_METEOR
# - 
# À noter, les variables d'environnement correspondantes existent dans le fichier ["./operations.sh"] et sont 
# liées au valeur déclarées pour les variables correspondantes dans le fichier ["./.env"]
FROM marguerite/meteor-stack:1.0.0

# Labels éditeur/distributeur

ARG MARGUERITE_USER_NAME=jbl-devops
ENV MARGUERITE_USER_NAME=$MARGUERITE_USER_NAME

ARG WORKSPACE_IDE=/marguerite/ide/esp-travail/
ENV WORKSPACE_IDE=/marguerite/ide/esp-travail/

ARG NOM_PROJET_MARGUERITE_METEOR=jbl-projet-meteor
ENV NOM_PROJET_MARGUERITE_METEOR=jbl-projet-meteor

# WORKDIR $WORKSPACE_IDE

# EXPOSE $MARGUERITE_METEOR_PORT/tcp
EXPOSE $MARGUERITE_METEOR_PORT/tcp
# ---- EXECUTION ---- #
# ++ PRINCIPE : 
# => NIVEAU IMAGE DE CONTENEUR STACK-METEOR :: On installe NVM, NODEJS, et METEOR, et donc il suffit de changer les variables d'environnement, au docker build, pour changer les versions du stack meteor
# => NIVEAU IMAGE DE CONTENEUR fille :: On créé les projets suivants dans le workspace : on créé le projet meteor , au niveau du ENTRYPOINT aussi
# => NIVEAU ENTRYPOPINT :  on fait le meteor run --port 
# => NIVEAU CMD :  CMD ["/bin/bash"] , aisni l'ide pourra exécuter n'importe quelle commande, en taznt que user linux $MARGUERITE_USER_NAME 


USER root
# Le [./point-d-entree.sh] sera toujours exécuté avant la 'CMD', et seule la CMD est surchargée par l'invocation [docker exec ... /chemin/dans/conteneur/vers/executable]
ADD marguerite-healthcheck.sh $WORKSPACE_IDE/$NOM_PROJET_MARGUERITE_METEOR/
RUN chmod a+x $WORKSPACE_IDE/$NOM_PROJET_MARGUERITE_METEOR/marguerite-healthcheck.sh
RUN chown -R $MARGUERITE_USER_NAME:$SUODERS_SYSTEM_GROUP $WORKSPACE_IDE
RUN echo " ----------------------------------- "
RUN echo " VERIF WORKSPACE_IDE=$WORKSPACE_IDE"
RUN echo " VERIF NOM_PROJET_MARGUERITE_METEOR=$NOM_PROJET_MARGUERITE_METEOR"
RUN echo " VERIF REP HEALTHCHECK=$WORKSPACE_IDE/$NOM_PROJET_MARGUERITE_METEOR/"
RUN echo " ----------------------------------- "
RUN echo " METEOR - IDE ==>> contenu du répertoire WORKSPACE_IDE, s'il existe  : "
RUN ls -all $WORKSPACE_IDE
RUN echo " ----------------------------------- "
RUN echo " METEOR - IDE ==>> contenu du répertoire NOM_PROJET_MARGUERITE_METEOR, s'il existe  : "
RUN ls -all $WORKSPACE_IDE/$NOM_PROJET_MARGUERITE_METEOR/
RUN echo " ----------------------------------- "
# RUN exit 2


USER $MARGUERITE_USER_NAME
HEALTHCHECK --interval=1s --timeout=30s --retries=150 CMD $WORKSPACE_IDE/$NOM_PROJET_MARGUERITE_METEOR/marguerite-healthcheck.sh
ENTRYPOINT $WORKSPACE_IDE/point-d-entree.sh
CMD ["/bin/sh"]
