![WildRig logo](assets/wildrig-logo.svg)\
**GPU mining for NVIDIA, AMD and Intel on Windows and Linux.**

Pick an algorithm, enter your pool and wallet, and start mining. WildRig brings live GPU statistics, automatic intensity, backup pools and temperature protection into one terminal window. You can also use plain logs and a JSON statistics API for rig monitoring.

Algorithm availability depends on your GPU, driver and miner version. Check the compatibility tables below before starting.

## Contents

- [Quick start](#quick-start)
- [Supported graphics cards](#supported-graphics-cards)
- [Supported algorithms](#supported-algorithms)
- [Everyday examples](#everyday-examples)
- [HiveOS, mmpOS and SimpleMining](#hiveos-mmpos-and-simplemining)
- [Command-line options](#command-line-options)
- [Reading the mining screen](#reading-the-mining-screen)
- [Troubleshooting](#troubleshooting)

## Quick start

You need a supported GPU with its vendor driver installed, a wallet for the coin you want to mine, and the pool's connection details. For AMD and Intel cards, install a driver that includes OpenCL support. AMD Instinct accelerators need Linux with ROCm and its OpenCL runtime.

1. Extract the miner into a folder.
2. Open a terminal in that folder, or create a launch script using an example below.
3. Replace `POOL_HOST`, `PORT` and `YOUR_WALLET` with the details supplied by your pool. Choose the correct `--algo` from the algorithm table.
4. Start the miner and look for **accepted shares**.

**Windows — save as `start.bat` next to `wildrig-ng.exe`:**

```bat
@echo off
setlocal
cd /d "%~dp0" || exit /b 1

:restart
wildrig-ng.exe --algo kawpow --url stratum+tcp://POOL_HOST:PORT --user YOUR_WALLET --worker rig01 --pass x

timeout /t 5 /nobreak >nul
goto restart
```

**Linux — save as `start.sh` next to `wildrig-ng`:**

```sh
#!/bin/sh

cd -- "$(dirname -- "$0")" || exit 1
trap 'exit 0' INT TERM

while true; do
    ./wildrig-ng --algo kawpow --url stratum+tcp://POOL_HOST:PORT --user YOUR_WALLET --worker rig01 --pass x

    sleep 5
done
```

All pool addresses and wallets in this guide are placeholders. On Windows, double-click `start.bat`. On Linux, run `chmod +x wildrig-ng` once, then `sh start.sh`. The scripts restart the miner five seconds after it exits.

The worker name identifies your rig on the pool. WildRig sends it as `user.worker`; follow your pool's login format and avoid adding the same worker both to `--user` and to `--worker`. Some pools require a specific password instead of `x`.

Press **Ctrl+C** to stop a launch script (confirm on Windows if prompted). In the full-screen interface, **q** exits the current miner run; the script restarts it after five seconds. For all options supported by your executable, run `wildrig-ng.exe --help` on Windows or `./wildrig-ng --help` on Linux.

To keep mining after disconnecting from SSH, run the Linux script in `screen` or `tmux`, or start it with `nohup sh start.sh > start.log 2>&1 &`.

## Supported graphics cards

Find your card below, then check the [supported algorithms](#supported-algorithms) to see what it can mine.

### NVIDIA

| Generation | Graphics cards |
| --- | --- |
| Pascal | GeForce **GTX 10 series** |
| Volta | **TITAN V** |
| Turing | GeForce **GTX 16 series**, **RTX 20 series**, NVIDIA **CMP 30HX**, **CMP 50HX** |
| Ampere | GeForce **RTX 30 series**, NVIDIA **CMP 70HX**, **CMP 90HX**, **CMP 170HX** |
| Ada Lovelace | GeForce **RTX 40 series** |
| Blackwell | GeForce **RTX 50 series** |

#### NVIDIA Data Center GPUs

| Architecture | Supported models |
| --- | --- |
| **Pascal** | Tesla **P4**, **P40**, **P100** |
| **Volta** | Tesla **V100** |
| **Turing** | NVIDIA **T4** |
| **Ampere** | NVIDIA **A2**, **A10**, **A16**, **A30**, **A40**, **A100**, **A800** |
| **Ada Lovelace** | NVIDIA **L4**, **L40**, **L40S** |
| **Hopper** | NVIDIA **H20**, **H100**, **H200**, **H800** |
| **Blackwell** | NVIDIA **B100**, **B200**, **B300** |

Available algorithms depend on the model and free GPU memory.

### AMD Radeon

| Generation | Graphics cards |
| --- | --- |
| Polaris | Radeon **RX 470 / 480 / 570 / 580 / 590** |
| Vega | Radeon **RX Vega 56 / 64**, **Radeon VII** |
| RDNA 1 | Radeon **RX 5000 series** |
| RDNA 2 | Radeon **RX 6000 series** |
| RDNA 3 | Radeon **RX 7000 series** |
| RDNA 4 | Radeon **RX 9000 series** |

Older **GCN 2/3** cards, including Hawaii and Fiji models, can use **KawPow** and **ProgPowZ**. The other listed algorithms require newer AMD cards.

#### AMD Instinct accelerators

| Architecture | Supported models |
| --- | --- |
| **CDNA 1** | Instinct **MI100** |
| **CDNA 2** | Instinct **MI210**, **MI250**, **MI250X** |
| **CDNA 3** | Instinct **MI300A**, **MI300X**, **MI308X**, **MI325X** |
| **CDNA 4** | Instinct **MI350X**, **MI355X** |

Instinct accelerators run on **Linux with ROCm** only. They can mine every AMD algorithm except **PearlHash** and **Noid**. An MI250 or MI250X appears as two GPUs. The MI300A is used like a discrete card, although its memory is shared with the CPU.

### Intel Arc

| Generation | Graphics cards |
| --- | --- |
| Alchemist | Arc **A-series**, including **A310–A770** |
| Battlemage | Arc **B570 / B580** |

Selected **Arc Pro** and **Flex** cards are also recognized. Check the algorithm table for Intel availability.

### Memory and integrated graphics

- **Available algorithms vary by card.** Check the algorithm table and compatibility notes before starting.
- **Free video memory matters.** Some algorithms need a large dataset called a DAG. Its size depends on the coin and block height, so there is no single minimum memory requirement for every algorithm. Other GPU applications reduce the memory available for mining.
- **Integrated graphics are skipped by default, except AMD BC-250.** BC-250 does not need `--gpu-allow-igpu`. Use this option to enable other eligible integrated GPUs. Every GPU still needs to support the selected algorithm and have enough available memory.

## Supported algorithms

Use the name in the first column with `--algo`. **Yes** means supported on compatible cards from that vendor, subject to the notes below. **—** means unavailable. Your card also needs enough free memory for the selected algorithm. The fee column shows the rate built into this version. A signed fee update can change the active rate; check the dashboard for the rate currently in effect.

| Algorithm | Coin | Developer fee | NVIDIA | AMD | Intel Arc |
| --- | --- | --- | --- | --- | --- |
| `autolykos2` | Ergo | 0% | Yes | Yes | — |
| `kawpow` | Ravencoin | 1% | Yes | Yes | Yes |
| `nexapow` | Nexa | 1% | Yes | Yes | Yes |
| `noid` | Parano1d | 2% | Volta or newer | RDNA 1–4 | — |
| `octopus` | Conflux | 0% | Yes | — | — |
| `pearlhash` | Pearl | 0% | Pascal or newer (except Tesla P100) | RDNA 1–4 | — |
| `progpowz` | Zano | 1% | Yes | Yes | Yes |
| `qhash` | Qubitcoin | 2% | Yes | Yes | Yes |
| `quantus` | Quantus | 2% | Yes | Yes | Yes |
| `sha256d` | Bitcoin | 0% | Yes | Yes | Yes |
| `xelishashv3` | Xelis | 1% | Yes | Yes | Yes |

Compatibility notes:

- NVIDIA support generally starts with Pascal (GTX 10 series).
- AMD support starts with Polaris for algorithms other than KawPow/ProgPowZ.
- Noid supports RDNA 1–4 with an OpenCL driver. It has been validated on RX 5600 XT, RX 6800 XT, RX 7900 XTX and RX 9070 XT. Other models in these families, including RX 9060 XT (`gfx1200`), have not yet been validated.
- AMD Instinct accelerators (CDNA 1–4) need Linux with ROCm and support every AMD algorithm except PearlHash and Noid.
- Autolykos2 currently needs at least 8 GB of GPU memory, and its requirement grows over time. Check that your card has enough free memory before mining.
- Use the pool address and port provided for your chosen coin and algorithm.

## Everyday examples

For mining examples below, replace the miner command inside the `start.bat` or `start.sh` loop above. On Windows, change `./wildrig-ng` to `wildrig-ng.exe`. Run benchmark examples separately because they have a fixed timeout.

### Add a backup pool

```sh
./wildrig-ng --algo kawpow --url stratum+tcp://PRIMARY_POOL:PORT --user YOUR_WALLET --worker rig01 --pass x --url stratum+tcp://BACKUP_POOL:PORT --pool-try-main
```

Pools are tried in the order listed. A backup inherits the primary pool's login, password and worker unless you specify its own immediately after its `--url`. For different accounts:

```sh
./wildrig-ng --algo kawpow --url PRIMARY_POOL:PORT --user FIRST_WALLET --pass x --url BACKUP_POOL:PORT --user SECOND_WALLET --pass x
```

By default, the miner stays on a working backup. `--pool-try-main` checks the primary about once a minute and returns when it becomes available. All listed pools should serve the selected algorithm.

### Select GPUs

```sh
./wildrig-ng --algo qhash --url POOL_HOST:PORT --user YOUR_WALLET --gpu-list 0,2
```

Use the GPU indices displayed by the miner. To use only NVIDIA cards, add `--gpu-no-amd --gpu-no-intel`; equivalent vendor switches are available for other rigs.

### Run an offline benchmark

```sh
./wildrig-ng --algo qhash --benchmark-timeout 60
```

This runs for 60 seconds without a pool, wallet or developer-fee session. For a DAG benchmark at a specific block:

```sh
./wildrig-ng --algo kawpow --benchmark-block 5000000 --benchmark-timeout 120
```

Startup, DAG generation and tuning take time. Let performance settle before comparing results; benchmark hashrate does not measure pool acceptance.

### Save logs and expose rig statistics

```sh
./wildrig-ng --algo qhash --url POOL_HOST:PORT --user YOUR_WALLET --ui-plain --log-file miner.log --api-port 4068 --api-worker-id rig01
```

Read JSON statistics at `http://127.0.0.1:4068/2/summary`. The API binds to **all network interfaces**, so it can also be reached through the rig's IP address. It has no authentication; use it on a trusted network and restrict access with your firewall. The API worker ID labels monitoring data and does not change the pool login.

## HiveOS, mmpOS and SimpleMining

Download the shared `wildrig-ng-custom-VERSION.tar.gz` from the [latest release](https://github.com/andru-kun/wildrig-ng/releases/latest). One archive contains the HiveOS and mmpOS integration scripts; each platform uses its own hooks. Both report GPU statistics automatically and use Bash, curl and jq; Python is not required on the rig.

### HiveOS flight sheet

Select **Custom** as the miner and enter:

- **Miner name:** `wildrig-ng-custom`.
- **Installation URL:** the direct download link for the shared custom-miner archive.
- **Hash algorithm:** the algorithm you want to mine, such as `qhash`; may be blank when set in Extra config.
- **Wallet and worker template:** `%WAL%.%WORKER_NAME%`, unless your pool requires another login format.
- **Pool URL / Pass:** your pool address and password, usually `x`.
- **Extra config arguments:** optional settings such as `--gpu-list 0,2 --gpu-intensity 20,20`.

If you leave **Hash algorithm** blank, set it in **Extra config arguments**, for example `--algo quantus`.

You can enter multiple pool URLs separated by spaces or newlines to add backups. The package manages logging and the API connection; do not add `--api-port` or `--log-file` to Extra config.

### mmpOS miner profile

Select **Custom miner**, enter the same shared archive download link, and choose your wallet and pool. Use:

```text
./mmp-launch.sh --coin %coin% %pool_protocol% --pool %pool_server%:%pool_port% --user %user% --password %password% --api-port %api_port%
```

Statistics cover the selected GPUs, including accepted/rejected shares and PCI bus numbers. HiveOS also receives temperature and fan readings. Both packages use plain logs and let the mining OS handle restarts.

### SimpleMining Custom miner

Use `wildrig-ng-simplemining-VERSION.zip`. It contains the `wildrig-ng-custom/`
directory with the Linux executable named `miner` and the same four HiveOS
hook/config files. Select that ZIP as the Custom miner package.

## Command-line options

Options accept `--name value` or `--name=value`. Flags such as `--benchmark` take no value. Short forms are shown where available.

### Pool and wallet

| Option | What it does | Default |
| --- | --- | --- |
| `--algo`, `-a` NAME | Select an algorithm from the table above. | Required |
| `--url`, `-o` URL | Pool address. Repeat to add backups. `host:port` means TCP; use `stratum+ssl://host:port` for an encrypted connection when supported by your miner version. | Required for pool mining |
| `--user`, `-u` LOGIN | Wallet or account for the preceding pool. Backups inherit the primary login when omitted. | Required for pool mining |
| `--pass`, `-p` PASSWORD | Password for the preceding pool. | `x` |
| `--worker`, `-w` NAME | Worker name, sent as `user.worker`. | Unset |

### Connection and failover

| Option | What it does | Default |
| --- | --- | --- |
| `--pool-retries` N | Connection attempts per pool before moving to the next; at least 1. | `1` |
| `--pool-retry-pause` SECONDS | Pause between connection attempts; at least 1. | `5` |
| `--pool-try-main` | Periodically try the primary while mining on a backup. | Off |
| `--pool-max-rejects` N | Disconnect after N consecutive rejected shares and follow the pool retry or failover rules. Set `0` to disable. | `5` |
| `--pool-send-stale` | Always submit stale shares, overriding automatic pool detection. Pool acceptance is not guaranteed. | Off |
| `--pool-timeout` SECONDS | Connection and handshake timeout; greater than 0. | `10` |
| `--proxy` ADDRESS | SOCKS5 proxy: `host:port`, `socks5://user:password@host:port` or `socks5h://user:password@host:port`. | Unset |
| `--tls-verify` | Verify pool TLS certificates. | Off |
| `--dns-over-https` PROVIDER | Resolve pool hosts through `google`, `cloudflare` or `alibaba`; `off` uses system DNS. Ignored when `--proxy` is set (the proxy resolves pool hosts itself). | `off` |

Aliases for `--pool-send-stale`: `--send-stale`, `--send-stales`, `--pool-send-stales`. DNS aliases: `cf` for Cloudflare; `alidns` or `ali` for Alibaba; `none`, `0` or `system` for off.

The miner checks whether each pool accepts stale shares and adjusts submissions automatically. Use `--pool-send-stale` only if you need to override this behavior. `xelishashv3` uses its own stale-share policy.

### GPU selection

| Option | What it does | Default |
| --- | --- | --- |
| `--gpu-list`, `-d` LIST | Comma-separated GPU indices shown by the miner, such as `0,2,3`. List each GPU once. Alias: `--devices`. | All eligible GPUs |
| `--gpu-allow-igpu` | Include eligible integrated GPUs. AMD BC-250 is already included by default. | Off |
| `--gpu-no-amd` | Skip AMD cards. | Off |
| `--gpu-no-nvidia` | Skip NVIDIA cards. | Off |
| `--gpu-no-intel` | Skip Intel cards. | Off |
| `--gpu-no-cuda` | Disable NVIDIA mining; useful when its driver causes startup problems. | Off |
| `--gpu-no-opencl` | Disable AMD/Intel mining; useful when their drivers cause startup problems. | Off |

### Performance and temperature

| Option | What it does | Default |
| --- | --- | --- |
| `--gpu-intensity`, `-i` N or LIST | Control mining intensity (`1..31`). Higher values may use more GPU memory. Leave unset for automatic selection. | Automatic / algorithm-specific |
| `--gpu-limit-compute` N or LIST | Request `1..100` percent of GPU compute units. This is not a power limit. | `100` |
| `--qhash-kernel` 1/2 | Choose QHash mode `1` or `2`. Uses mode `1` if mode `2` is unavailable. Compare them with a benchmark. | `1` |
| `--pearlhash-kernel` 1/2 | Choose the PearlHash kernel: `1` is the default kernel, `2` the efficient kernel. GPUs without an efficient kernel mine with `1`. | Automatic |
| `--quantus-kernel` 1/2 | Choose the Quantus kernel: `1` is the default kernel, `2` the efficient kernel. GPUs without an efficient kernel mine with `1`. | `1` |
| `--noid-kernel` 1/2 | Choose the Noid kernel: `1` is the default kernel, `2` the efficient kernel. GPUs without an efficient kernel mine with `1`. | `1` |
| `--gpu-temp-limit` C | Pause a GPU at this temperature; accepts `1..150`. `0` turns the thermal guard off: neither overheating nor a temperature sensor error pauses mining. | `90` |
| `--gpu-temp-resume` C | Resume below the pause threshold; must be lower than `--gpu-temp-limit`. | `65` |

Start with automatic intensity. Higher values do not always improve hashrate and may use more GPU memory. If compute limiting is unavailable, the miner warns and uses the full GPU. Temperature protection requires working hardware monitoring.

### Clocks, power and fans

These controls are available for supported NVIDIA cards. For AMD and Intel, use your usual GPU tuning software. Some NVIDIA settings require running the miner as administrator on Windows or as root on Linux. Check the log to see whether each setting was applied.

For options accepting a list, one value applies to all selected GPUs. Multiple values follow the **selected GPU order**. For example, `--gpu-list 2,0 --gpu-powerlimit 180,120` requests 180 W on GPU 2 and 120 W on GPU 0. These are syntax examples, not recommended limits for every card.

| Option | What it does | Accepted range / default |
| --- | --- | --- |
| `--gpu-core-clock` N or LIST | Lock core clock in MHz. | `0..10000`; unset |
| `--gpu-core-offset` N or LIST | Core clock offset in MHz. | `-2000..2000`; unset |
| `--gpu-memory-clock` N or LIST | Lock memory clock in MHz. | `0..20000`; unset |
| `--gpu-memory-offset` N or LIST | Memory clock offset in MHz. | `-5000..5000`; unset |
| `--gpu-powerlimit` N or LIST | Power limit in watts. | `1..2000`; unset |
| `--gpu-fan-speed` N or LIST | `0` restores driver control; `1..100` sets fixed fan speed in percent, retained after exit. | `0..100`; unset |
| `--gpu-fan-target` C or LIST | Adjust fan speed to target the GPU core temperature. Overrides fixed fan speed on the corresponding GPU; pauses that GPU if core temperature is unavailable. Restores each fan's previous policy and fixed speed on normal shutdown. Use with caution. | `1..150` °C; unset |
| `--gpu-fan-min` N or LIST | Minimum fan speed for automatic control. Requires a fan target. | `0..100`%; default `10` |
| `--gpu-fan-max` N or LIST | Maximum fan speed for automatic control. Requires a fan target. | `0..100`%; default `100` |
| `--gpu-reset-oc` | Unlock core/memory clocks and reset offsets at startup. Does not reset power limits or fans. | Off |
| `--gpu-delay-oc` SECONDS | Delay applying tuning settings after startup. | `0..3600`; default `0` |

Your card may support a smaller range than the options accept. Check the log to confirm which settings the driver applied. Clock settings may be delayed or briefly reset during DAG generation.

For example, `--gpu-fan-target 65 --gpu-fan-min 30 --gpu-fan-max 85` aims for 65 °C with fan speed between 30% and 85%. Values follow the selected GPU order. The miner warns about invalid ranges; a GPU may still exceed the target temperature if the maximum fan speed is insufficient.

With `--gpu-fan-target`, the miner adjusts the fan while it runs and restores its previous setting on normal shutdown. A fixed `--gpu-fan-speed 1..100` remains set after exit; use `--gpu-fan-speed 0` to return control to the driver. Fan control is currently available for NVIDIA GPUs only.

A fan target needs a working core temperature sensor. If its reading is unavailable, mining pauses on that GPU; a stalled driver read or clock change can pause all GPUs. Check the log. Mining resumes when the driver responds and the cards cool. Setting `--gpu-temp-limit 0` disables these safety pauses, so mining can continue without reliable temperature readings and the fan target uses its maximum speed.

### Interface and logging

| Option | What it does | Default |
| --- | --- | --- |
| `--ui-plain` | Use plain logs instead of the full-screen dashboard. Also used automatically when stdout is redirected or no interactive terminal is available. | Dashboard when available |
| `--ui-wide` | Expand pool and GPU tables to the available console width. Also applies to `--ui-plain`; otherwise the tables use their natural content width. | Off |
| `--ui-raw-diff` | Display raw pool difficulty instead of expected hashes per share. | Off |
| `--ui-no-pool-list` | Hide the pool table. | Off |
| `--log-file` PATH | Append logs and a GPU status report every minute to a file. | Unset |
| `--print-debug` | Include extra details for troubleshooting. Logs may contain wallet/login information. | Off |
| `--help`, `-h` | Print help and exit. | — |
| `--version`, `-V` | Print version and exit. | — |

A GPU that is still starting appears as `INIT`. Use `--ui-plain` to see driver startup messages.

### Monitoring and recovery

| Option | What it does | Default |
| --- | --- | --- |
| `--api-port` PORT | Enable HTTP JSON statistics on port `1..65535`. | `0` = off |
| `--api-worker-id` NAME | Rig name reported by the API. | Machine hostname |
| `--no-watchdog` | Turn off the built-in watchdog: a failed GPU stays in ERROR while the others mine, and the miner stays idle if every GPU fails. | Watchdog on |
| `--watchdog-log` PATH | Set where the watchdog records failed GPUs and miner crashes. | `watchdog_log.txt` beside the executable |
| `--no-adl` | Disable AMD temperature, fan and power monitoring. | Off |
| `--no-nvml` | Disable NVIDIA temperature, fan and power monitoring. | Off |
| `--no-igcl` | Disable Intel temperature, fan and power monitoring. | Off |

On Windows, AMD sensor readings depend on the GPU and driver; unsupported readings show `n/a`. For troubleshooting, use `--print-debug --log-file miner.log` to record sensor availability.

A GPU shows **STUCK** when mining, job preparation or startup stops making progress. The miner attempts recovery where possible. Check the log, driver, clocks and available memory if the GPU stays STUCK.

The built-in watchdog is on by default, except in benchmark mode; you may see two `wildrig-ng` processes. After an NVIDIA driver failure that requires a restart, it restarts the miner and excludes the failed GPU for the rest of that run. The GPU appears as **ERROR**, and the reason is recorded in the watchdog log. Close and reopen the miner to retry an excluded GPU.

If a GPU hangs in the driver, it stays **STUCK** while healthy GPUs continue mining; that alone does not restart the miner. An AMD or Intel GPU failure can likewise leave the other GPUs mining. If no GPU is detected at startup, the watchdog retries detection. If every GPU fails, the miner stays idle. With `--no-watchdog`, failed GPUs stay in **ERROR** until the miner is restarted.

Disabling hardware monitoring may also remove temperature, fan and power readings or controls.

### Offline benchmarking

| Option | What it does | Default |
| --- | --- | --- |
| `--benchmark` | Measure mining speed without connecting to a pool; requires `--algo`. | Off |
| `--benchmark-epoch` N | Enable benchmarking and select a DAG epoch where applicable. | Algorithm default |
| `--benchmark-block` N | Enable benchmarking and select a block height; overrides the epoch option. | Algorithm default |
| `--benchmark-timeout` SECONDS | Enable benchmarking and exit after this duration. | `0` = until stopped |

Each `--benchmark-*` option enables benchmark mode by itself. Any pool settings are ignored in this mode.

## Reading the mining screen

The dashboard shows GPU hashrates, available sensor readings, share counters, pool information and a scrolling log.

- **Accepted:** the pool accepted your share. This is the main indication that mining is working.
- **Rejected:** the pool refused a submitted share. Repeated rejects need investigation; check the reason in the log.
- **Stale / ignored:** work became outdated before submission. Network latency and frequent job changes can contribute.
- **Hashrate:** local mining speed. Pool estimates fluctuate because they are calculated from submitted shares over time.
- **DAG generation / tuning:** preparation before normal mining speed is reached. Allow it to complete before comparing performance.
- **STUCK:** the GPU has stopped making mining progress; the miner attempts to reinitialize it. See [Monitoring and recovery](#monitoring-and-recovery).
- **ERROR:** the GPU failed; check the log for the reason.

Use **Page Up / Page Down**, **Up / Down**, **Home / End** or the mouse wheel to scroll the log. A paused hot GPU resumes after cooling to its configured resume threshold.

## Troubleshooting

| Symptom | What to check |
| --- | --- |
| No usable GPU found | Install the vendor driver with OpenCL support for AMD/Intel, and check GPU selection options. Integrated GPUs are skipped by default except AMD BC-250. With the watchdog enabled, the miner retries detection automatically. |
| Missing kernel / unsupported GPU | Check the algorithm and family tables. Your card may be detected but unable to run the chosen algorithm. Check that your miner version supports this combination. |
| Not enough GPU memory / DAG allocation failed | Close other GPU applications and check the block/epoch memory requirement. Lower intensity may reduce memory use, but cannot reduce the size of the required DAG. |
| Cannot connect to a pool | Check hostname, port, TCP versus TLS, proxy settings and network access. Add a backup pool. |
| `Can't connect to a devfee pool` | Check network access, DNS, TLS and proxy settings, and the system clock. The miner retries automatically. |
| Login rejected | Check the coin's wallet format, pool account rules, worker syntax and required password. |
| Repeated invalid shares / self-test failure | Return clocks and offsets to stock, check driver compatibility and read the exact error. |
| GPU pauses at the temperature limit | Check cooling, fans and power settings. Mining resumes after cooling; sensor support is required. |
| Tuning setting rejected | Check privileges and driver/card support. The log reports failures for individual settings. |
| Dashboard looks wrong in a service or terminal | Add `--ui-plain`; use `--log-file` to retain output. |
| A parameter seems to have no effect | Read startup warnings and run `--help`. Invalid settings may be ignored or stop startup. |

When reporting a problem, include the miner version, operating system, GPU model, driver version, algorithm and relevant log lines. Remove passwords and proxy credentials before sharing logs.

---

**Made in Ukraine** ![Flag of Ukraine](assets/ukraine.svg)
