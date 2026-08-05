# rama-digitalocean

Desired state for one private Rama 1.9.0 node in DigitalOcean Amsterdam.
ZooKeeper, Conductor, Supervisor, and Rama service ports are available only over
WireGuard. Public ingress is SSH from the configured operator CIDR and
WireGuard UDP. `rama.bigconfig.online` is DNS-only; Resend configures
`notifications.bigconfig.online` and machine mail.

```sh
./green build
./green create --dry-run
./green create
./green rama conductorReady
./green rama numSupervisors
```

Credentials belong only in `.envrc.private`. Never set `COLORS_PAR_PROFILE`.
The committed destroy guard remains true. For an authorized deletion of this
deployment only:

```sh
COLORS_PAR_COMPUTE_PREVENT_DESTROY=false ./green delete
```
