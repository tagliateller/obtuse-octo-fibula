* Installation Nexus usw.

Quelle: https://github.com/jorgemoralespou/nexus-ose sowie https://blog.openshift.com/improving-build-time-java-builds-openshift/

Nexus läuft, wird aber aktuell nicht durch die Sample-App genutzt.

oc new-app --docker-image=wildfly-nexus-9 --strategy=source --code=https://github.com/bparees/openshift-jee-sample.git --name='wildfly-nexus-sample'

die Sample-App selbst ist aber lauffähig

* TODO Builder-Images prüfen, diese wurden so installiert:

oc new-project wildfly-nexus-builds --display-name="Wildfly builds with Nexus" --description="Building Applications in Wildfly using Nexus for dependency management"

oc create -f builders/wildfly-nexus/wildfly-nexus-resources.json
