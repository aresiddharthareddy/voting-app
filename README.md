Kubernetes Voting App – Cat vs Dog

Overview:
This is a sample voting application deployed on Kubernetes to practice deployments, services, and networking.
Users can vote for Cat 🐱 or Dog 🐶, and see live results.

The app has 5 main components, each running in its own pod:

Component	Image	Purpose
vote	dockersamples/examplevotingapp_vote	Frontend for users to vote. Stores votes in Redis.
redis	redis:alpine	In-memory database to temporarily store votes.
worker	dockersamples/examplevotingapp_worker	Background processor; moves votes from Redis → Postgres.
db	postgres:9.4	Persistent database to store final vote counts.
result	dockersamples/examplevotingapp_result:after	Frontend to display live voting results from Postgres.
Kubernetes Setup

Namespace: siddhartha – all resources run inside this namespace.

Deployments: Each component has its own Deployment to manage pods and replicas.

Services:

vote → NodePort (exposed outside the cluster for voting)

result → NodePort (exposed for results UI)

redis & db → ClusterIP (internal access only)

Volumes:

db pod uses emptyDir in lab setup (for storage) to simplify deployment.

Deployment Order

Redis → backend cache

Postgres (db) → persistent vote storage

Worker → moves votes from Redis → Postgres

Vote → frontend for users to vote

Result → frontend to display results

Commands
Apply YAMLs
kubectl apply -f redis.yaml
kubectl apply -f db.yaml
kubectl apply -f worker.yaml
kubectl apply -f vote.yaml
kubectl apply -f result.yaml

Verify Pods & Services
kubectl get pods -n siddhartha
kubectl get svc -n siddhartha

Access Apps

Vote: http://<NodeIP>:30004

Result: http://<NodeIP>:30005

Debugging
kubectl logs -n siddhartha <pod-name>
kubectl exec -it -n siddhartha <pod-name> -- /bin/sh

How It Works

User votes in vote app → stored temporarily in redis.

Worker pod fetches votes from redis → inserts them into db.

Result pod reads votes from db → displays live results in frontend.

Services connect pods internally (ClusterIP) or externally (NodePort).

Learning Focus

Deployments: scaling & updating pods

Services: ClusterIP, NodePort

Environment Variables: pod communication

Namespaces: isolation

Pod connectivity & DNS: internal pod communication

NodePort access: exposing frontend outside cluster
