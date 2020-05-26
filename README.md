# APPD_Implementation_Mashery
APPD_Implementation_Mashery

Here is an example of AppD instrumentation with Tibco Mashery where the agent is embedded, and the configurable values are passed as environment variables by means of a configMap, a secret and the pod spec.


Config Map with APPD Enviornment Var values defined:

apiVersion: v1
kind: ConfigMap
metadata:
  name: appd-config
data:
  APPDYNAMICS_AGENT_APPLICATION_NAME: "mashery"
  APPDYNAMICS_AGENT_SSL_PROTOCOL: "TLSv1.2"
  APPDYNAMICS_CONTROLLER_SSL_ENABLED: "true"
  APPDYNAMICS_CONTROLLER_HOST_NAME:
  APPDYNAMICS_CONTROLLER_PORT:
  APPDYNAMICS_DOCKER_ENABLED: "true"
  APPDYNAMICS_SIM_ENABLED: "true"
  APPDYNAMICS_JAVA_AGENT_REUSE_NODE_NAME: "true"
  APPDYNAMICS_JAVA_AGENT_REUSE_NODE_NAME_PREFIX: "pod"
  APPDYNAMICS_AGENT_ENABLED: "true"


Secret created for APPD License :

apiVersion: v1
kind: Secret
metadata:
  name: appd-secret
type: Opaque
data:
  #base64 encoded APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY (echo -n <keyvalue>|base64)
  APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY:

  
  init container was used to place appd jar file :
  
  initContainers:
      - command:
        - cp
        - -r
        - /sharedFiles/AppServerAgent
        - /mountPath/AppServerAgent
        image:  <docker registery>/appd-java:4.5.14.4
        imagePullPolicy: IfNotPresent
        name: appd-java
        volumeMounts:
        - mountPath: /mountPath
          name: appd-java
  
  
  Pod spec for appd :
  
  - name: APPDYNAMICS_AGENT_TIER_NAME
            value: "trafficmanager"
          - name: APPDYNAMICS_AGENT_NODE_NAME
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
        envFrom:
        - configMapRef:
            name: appd-config
        - secretRef:
            name: appd-secret
  
  



