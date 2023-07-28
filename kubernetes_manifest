apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    artifact.spinnaker.io/location: default
    artifact.spinnaker.io/name: datascience-tensorflow-serving
    artifact.spinnaker.io/type: kubernetes/deployment
    artifact.spinnaker.io/version: ""
    deployment.kubernetes.io/revision: "4"
    moniker.spinnaker.io/application: tensorflow-serving
    moniker.spinnaker.io/cluster: deployment datascience-tensorflow-serving
  labels:
    app: datascience-tensorflow-serving
    app.kubernetes.io/managed-by: spinnaker
    app.kubernetes.io/name: tensorflow-serving
  name: datascience-tensorflow-serving
  namespace: default
spec:
  progressDeadlineSeconds: 600
  replicas: 20
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: datascience-tensorflow-serving
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      annotations:
        artifact.spinnaker.io/location: default
        artifact.spinnaker.io/name: datascience-tensorflow-serving
        artifact.spinnaker.io/type: kubernetes/deployment
        artifact.spinnaker.io/version: ""
        moniker.spinnaker.io/application: tensorflow-serving
        moniker.spinnaker.io/cluster: deployment datascience-tensorflow-serving
      creationTimestamp: null
      labels:
        app: datascience-tensorflow-serving
        app.kubernetes.io/managed-by: spinnaker
        app.kubernetes.io/name: tensorflow-serving
    spec:
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - preference:
              matchExpressions:
              - key: cpu_family
                operator: In
                values:
                - n2
                - t2d
            weight: 100
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: purpose
                operator: In
                values:
                - tensorflow-serving
              - key: num_cpus
                operator: In
                values:
                - "20"
                - "32"
      containers:
      - args:
        - --port=8500
        - --rest_api_port=8501
        - --model_config_file=/config/model.config
        - --allow_version_labels_for_unavailable_models
        - --enable_model_warmup
        - --tensorflow_intra_op_parallelism=1
        - --tensorflow_inter_op_parallelism=6
        - --file_system_poll_wait_seconds=60
        - --enable_batching
        - --batching_parameters_file=/config/batching.config
        - --monitoring_config_file=/config/monitoring.config
        - --grpc_channel_arguments=grpc.max_connection_age_ms=240000,grpc.max_connection_age_grace_ms=30000
        command:
        - /usr/bin/tensorflow_model_server
        image: gcr.io/ox-registry-prod/tensorflow/serving:2.6.2
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 10
          initialDelaySeconds: 240
          periodSeconds: 10
                    successThreshold: 1
          tcpSocket:
            port: 8500
          timeoutSeconds: 1
        name: tensorflow-serving
        ports:
        - containerPort: 8501
          name: http
          protocol: TCP
        - containerPort: 8500
          name: grpc
          protocol: TCP
        readinessProbe:
          failureThreshold: 10
          initialDelaySeconds: 120
          periodSeconds: 20
          successThreshold: 1
          tcpSocket:
            port: 8500
          timeoutSeconds: 1
        resources:
          limits:
            cpu: "18"
            memory: 9G
          requests:
            cpu: "18"
            memory: 9G
        executionMessagePath: /dev/execution-log
        executionMessagePolicy: File
        volumeMounts:
        - mountPath: /config
          name: tensorflow-serving-config
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: tensorflow-serving
      serviceAccountName: tensorflow-serving
      executionGracePeriodSeconds: 30
      tolerations:
      - effect: NoSchedule
        key: tensorflow-serving
        operator: Exists
      volumes:
      - configMap:
          defaultMode: 420
          name: datascience-tensorflow-serving-v002
        name: tensorflow-serving-config
status:
