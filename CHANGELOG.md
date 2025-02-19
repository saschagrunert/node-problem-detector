# Change Log

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](http://keepachangelog.com/)
and this project adheres to [Semantic Versioning](http://semver.org/).

## [Unreleased]

## [0.8.7] - 2020-02-18

### Added

- Add memory read error.
- Add support for building NPD on MacOS.
- Add support for containerd health check.
- Add metric for `per-cpu`, `per-stage` timing.

### Fixed

- Fix an issue that kubelet may be restarted by NPD health checker unexpectedly. Make log pattern check configurable in health checker.
- Exit the process when there is a timeout in plugin daemon


## [0.8.6] - 2020-01-22

### Added

- Windows build now supported.
- Added metrics to retrieve stats such as `procs_running` and `procs_blocked`.
- Added metrics to retrieve network stats.
- Added metric to retrieve guest OS features such as unknown modules, ktd,
  and kernel integrity.

### Changed

- Print result's message when status is unknown.

### Fixed

- Fixed custom plugin command timeout when the command spawns a long running
  child process.

## [0.8.5] - 2020-11-18

### Added

- Added problem detection for buffer I/O error.
- Added CPU load average metrics support.
- Added kubelet apiserver connection check in health checker.

### Changed

- Will now catch hung task with pattern like `tasks airflow scheduler: *`.
- Better handling to avoid duplicating disk bytes metrics used on fstype and
  mount types.

### Fixed

- Fixed the deployment yaml to prevent NPD from scheduling onto windows nodes.
- Fixed memory unit for `/proc/meminfo` metrics.
- Fixed OOMKilling detection for new linux kernel v5.1+.

## [0.8.4] - 2020-09-01

### Added

- Added `FSType` and `MountOption` as labels to the metric `disk_usage_bytes`.
- Added `DockerContainerStartupFailure` event in `docker-monitor.json` to
  detect docker issue
  [docker/for-linux#647](https://github.com/docker/for-linux/issues/647).

### Fixed

- Reduced log spam generated by the custom plugin monitor.

## [0.8.3] - 2020-06-30

### Added

- `health-checker` binary now included in the docker image.

### Changed

- `--enable-repair=true` is now the default for docker and kubelet health
  checker.
- Custom plugin will now only generate status update log when the status has
  changed.
- Limit the size of custom plugin output to 4kb, extra output will be drained
  and discarded.

### Fixed

- Fix a race condition that services may be killed periodically when
  `--enable-repair=true`, and systemd service restart time equals the health
  check period.

## [0.8.2] - 2020-05-28

### Added

- Added an `--event-namespace` flag to make event namespace configurable.
- Added `rhel` support in OS version.
- Added `health-checker` as a custom plugin. The `health-checker` can be used
  to monitor healthiness of kubelet, docker and CRI container runtimes (e.g.
  `containerd`, `cri-o`) and restart them if they are not healthy if
  `enable-repair` is turned on.

### Fixed

- [#420](https://github.com/kubernetes/node-problem-detector/issues/420) Added
  missing `lsblk` to the container image.

## [0.8.1] - 2020-02-25

### Added

- Added `host_uptime` metrics for CentOS.
- Now collecting a lot more useful CPU/disk/memory metrics.

### Changed

- Improved `network_problem.sh` to support `nf_conntrack` and report error when
  conntrack table is 90% full.

### Fixed

- [#366](https://github.com/kubernetes/node-problem-detector/issues/366) Fixed
  building with `ENABLE_JOURNALD=0`.
- Fixed the first 0 value metrics reported for `disk_avg_queue_len`.
- Fix a few metric units for disk metrics and the calculation for
  `disk_avg_queue_len`.

## [0.8.0] - 2019-10-30

### Added

- Added Stackdriver exporter.
- Added a `k8s-exporter-heartbeat-period` flag to make the heart beat period
  of K8s exporter configurable.

### Changed

- Changed the default heart beat period of K8s exporter from `1m` to `5m`.

### Fixed

- Addressed an issue with a panic caused by closing an already closed channel.
- Fixed several potential busy loops.

## [0.7.1] - 2019-08-27

### Added

- Added validation that permanent problems habe a preset default condition.

### Changed

- Empty LogPath will now use journald's default path.
- Systemd monitor now looks back 5 minutes.
- Bumped base image to `k8s.gcr.io/debian-base-amd64:1.0.0`.
- Updated the detection method for docker overlay2 issues.
- Moved NPD into the kube-system namespace.

### Fixed

- [#202](https://github.com/kubernetes/node-problem-detector/issues/202) Fixed
  an issue that condition can't switch back to false for custom plugins.

## [0.7.0] - 2019-07-25

### Added

- Added a system stats monitor is added into NPD as a new problem daemon. It
  collects useful node problem related system stats with OpenCensus such as
  `disk/io_time`, `disk/weighted_io` and `disk/avg_queue_len`.
- Besides node condition and events, problems detected by existing problem
  daemons are also collected into OpenCensus as metrics:
  `problem_counter{reason="PROBLEM_REASON"} xxx` for events and
  `problem_gauge{reason="PROBLEM_REASON",type="PROBLEM_TYPE"} 1 or 0` for
  conditions.
- A Prometheus exporter is added to export all OpenCensus metrics collected by
  NPD through Prometheus.
- A plugin system for problem daemons is added. Problem daemons can be disabled
  at compile time with build tags, such as `disable_system_stats_monitor`,
  `disable_system_log_monitor` and `disable_custom_plugin_monitor`.
- A problem exporter interface is added. The original kubernetes problem
  reporting logic was moved into `k8sexporter`. Prometheus support is
  implemented as `prometheusexporter`.

## [0.6.6] - 2019-08-13

### Changed

- Updated the detection method for docker overlay2 issues.

### Fixed

- [#202](https://github.com/kubernetes/node-problem-detector/issues/202) Fixed
  an issue that condition can't switch back to false for custom plugins.

## [0.6.5] - 2019-07-24

### Fixed

- [#295](https://github.com/kubernetes/node-problem-detector/issues/295) Added
  configurable timeout to wait for apiserver to be ready before starting
  problem detection.

## [0.6.4] - 2019-06-13

### Changed

- Switch from godep to go modules resulting in bumping versions of many
  dependencies.
- Changed custom plugin handling to run immediately on startup.

### Fixed

- [#269](https://github.com/kubernetes/node-problem-detector/issues/269) Fixed
  issue so that using `--version` should not require monitors to be specified.

## [0.6.3] - 2019-04-05

### Added

- Added better handling and reporting when missing required flags.

### Fixed

- Disabled glog writing to files for the log-counter plugin.

## [0.6.2] - 2019-01-07

### Added

- Added resource limites to NPD deployment.
- Added log-counter to dockerfile.
- Added `enable_message_change_based_condition_update` option to enable
  condition update when messages change for custom plugin.

### Fixed

- [#232](https://github.com/kubernetes/node-problem-detector/issues/232) Explicitly
  include libsystemd0 in the image.

## [0.6.1] - 2018-11-28

### Changed

- Bumped base image to `k8s.gcr.io/debian-base-amd64:0.4.0`.

## [0.6.0] - 2018-11-27

### Added

- Added ConfigMap for NPD config.
- Added readonly filesystem detection.
- Added frequent kubelet/docker restart detection.
- Added corrupt docker overlay2 issue detection.

### Changed

- Bumped Kubernetes client version to 1.9.
- Updated OOMKilling pattern to support new kernel.

## [0.5.0] - 2018-06-22

### Added

- Added custom problem detector plugin interface.
- Added custom network plugin monitor.
- Added a kernel log counter custom problem detector to detect problems which
  have the same pattern.

### Changed

- Changed default port from 10256 to 20256 to avoid conflict with kube-proxy.
- Bumped golang version from 1.8 to 1.9.
- Bumped base image to `k8s.gcr.io/debian-base-amd64:0.3`.

### Fixed

- Fixed an error in the labels applied to the daemonset label selector.

## [0.4.1] - 2017-06-21

### Added

- Added docker image pull error detection.

## [0.4.0] - 2017-04-31

### Added

- Added "kernel log generator" container for test purposes.
- Added ABRT adaptor config.

## [0.3.0] - 2017-03-15

### Added

- Added look back support in kernel monitor. Kernel monitor will look back for
  specified amount of time to detect old problems during each start or restart.
- Added support for running node-problem-detector standalone.
- Added `-hostname-override` option to provide custom node name.
- Added `-port` option to provide custom listening port for service.
- Added `-address` option to define binding address.
- Added journald support.
- Added travis presubmit test.
- Added arbitrary system log support.

### Changed

- Update kubernetes version to v1.4.0-beta.3

### Fixed

- Only change transition timestamp when condition has changed.
- [#47](https://github.com/kubernetes/node-problem-detector/issues/47) Don't
  report KernelDeadlock on `unregister_netdevice` event.
- [#48](https://github.com/kubernetes/node-problem-detector/issues/48) Use
  system boot time instead of "StartPattern".

## [0.2.0] - 2016-08-23

### Added

- Add support for some kernel oops detection.

### Changed

- Change NPD to get node name from `NODE_NAME` env first before `os.Hostname`,
  and update the example to get node name from downward api and set `NODE_NAME`.

## 0.1.0 - 2016-06-09

### Added

- Initial version of node problem detector.

[Unreleased]: https://github.com/kubernetes/node-problem-detector/compare/v0.8.6...HEAD
[0.8.6]: https://github.com/kubernetes/node-problem-detector/compare/v0.8.5...v0.8.6
[0.8.5]: https://github.com/kubernetes/node-problem-detector/compare/v0.8.4...v0.8.5
[0.8.4]: https://github.com/kubernetes/node-problem-detector/compare/v0.8.3...v0.8.4
[0.8.3]: https://github.com/kubernetes/node-problem-detector/compare/v0.8.2...v0.8.3
[0.8.2]: https://github.com/kubernetes/node-problem-detector/compare/v0.8.1...v0.8.2
[0.8.1]: https://github.com/kubernetes/node-problem-detector/compare/v0.8.0...v0.8.1
[0.8.0]: https://github.com/kubernetes/node-problem-detector/compare/v0.7.0...v0.8.0
[0.7.1]: https://github.com/kubernetes/node-problem-detector/compare/v0.7.0...v0.7.1
[0.7.0]: https://github.com/kubernetes/node-problem-detector/compare/v0.6.6...v0.7.0
[0.6.6]: https://github.com/kubernetes/node-problem-detector/compare/v0.6.5...v0.6.6
[0.6.5]: https://github.com/kubernetes/node-problem-detector/compare/v0.6.4...v0.6.5
[0.6.4]: https://github.com/kubernetes/node-problem-detector/compare/v0.6.3...v0.6.4
[0.6.3]: https://github.com/kubernetes/node-problem-detector/compare/v0.6.2...v0.6.3
[0.6.2]: https://github.com/kubernetes/node-problem-detector/compare/v0.6.1...v0.6.2
[0.6.1]: https://github.com/kubernetes/node-problem-detector/compare/v0.6.0...v0.6.1
[0.6.0]: https://github.com/kubernetes/node-problem-detector/compare/v0.5.0...v0.6.0
[0.5.0]: https://github.com/kubernetes/node-problem-detector/compare/v0.4.1...v0.5.0
[0.4.1]: https://github.com/kubernetes/node-problem-detector/compare/v0.4.0...v0.4.1
[0.4.0]: https://github.com/kubernetes/node-problem-detector/compare/v0.3.0...v0.4.0
[0.3.0]: https://github.com/kubernetes/node-problem-detector/compare/v0.2.0...v0.3.0
[0.2.0]: https://github.com/kubernetes/node-problem-detector/compare/v0.1.0...v0.2.0
