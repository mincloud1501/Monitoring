# Monitoring
오픈 소스 기반의 모니터링 시스템인 Prometheus에 대한 기본 개념과 실습 (using Katacoda)

- 오픈 소스 기반의 모니터링 시스템
- 관리 대상 시스템으로 부터 각종 모니터링 지표를 수집하여 저장하고 검색할 수 있는 시스템
- 구조가 간단해서 운영이 쉽고, 강력한 쿼리 기능을 가지고 있으며, Grafana를 통한 시각화를 지원한다.
- 넓은 오픈 소스 생태계를 기반으로 많은 시스템을 모니터링할 수 있는 다양한 플러그인을 가지고 있다. 
- 특히 k8s의 메인 모니터링 시스템으로 많이 사용되면서 요즘 특히 더 주목을 받고 있다.

## Basic Architecture

![architecture](images/architecture.png)

`√ Metric Collect`
- MySQL이나, Tomcat 또는 VM 과 같은 모니터링 대상에서 metric을 prometheus에 전송하기 위해 `Exporter`를 사용한다.

`√ Pulling`
- prometheus가 주기적으로 Exporter로 부터 메트릭 읽어와서 수집하는 방식
- Autoscaling에 의한 추가된 VM들의 IP를 알 수 없는 경우를 위해 service discovery 방식을 사용한다.

`√ Service Discovery`
- prometheus도 service discovery system과 통합을 하도록 되어 있다. DNS나, service discovery 전용 솔루션인 Hashicorp사의 Consul 또는 k8s를 통해서, target service의 list을 가지고 올 수 있다. 

`√ Exporter`
- Monitoring Agent로 target system에서 metric을 읽어 prometheus가 pulling할 수 있도록 한다.
- HTTP GET으로 해당 metric을 text형태로 return한다.

`√ Retrieval`
- service discovery system으로 부터 monitoring 대상 목록을 받아오고, Exporter로 부터 주기적으로 그 대상으로 부터 metric을 수집하는 module

`√ Save`
- 수집된 정보는 prometheus내의 memory와 local disk에 저장, 스케일링이 불가한 단점 존재
- 확장이 불가능하고, 저장 용량이 부족하면 디스크 용량을 늘리는 것 밖에 방안이 없다
- 구조상 HA를 위한 이중화나 clustering이 불가

`√ Serving`
- 저장된 metric은 `PromQL`을 이용하여 조회하고, 이를 외부 API나 prometheus Web Console을 이용하여 서빙이 가능

