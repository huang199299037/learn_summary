```
--gtest_filter=P21/suTestUnsqueeze*
cp -rf $SUDNN_PATH/build/suge-build/lib $SUGE_PATH/
cp -rf $SUDNN_PATH/build/suge-build/sucg-build/lib $SUCG_PATH/

echo "copy suge.so"
echo "copy sucg.so"
sshfs e00437@10.50.20.170:/home/e00437/vscode/sulib .
fusermount -u  /home/br104/e00437
fusermount -zu  /home/br104/e00437
_list_tests
git submodule update --init --recursive
repo sync --fetch-submodules
--gtest_output=(json|xml)
ssh br104@10.9.1.123

cd suGE/build/sucg-build/ && pip install ./

git submodule update --init

cmd_test_filter = '--gtest_filter=P101/suTestADD.bert/2'

grep "returncode is 0" client_log_file.log | wc -l

--gtest_filter=P13/suTestMul.bert/0

set -exo pipefail

export PLATFORM=br104
export EXEC_NAME="sucg;suge"
export CASE_NAME="P12/suTestLSTMCell.tacotron2/0;P21/suTestUnsqueeze.bert/0"
P113/suTestMma3d.BERT/10

物理cpu
# wc -l 是统计行数
cat /proc/cpuinfo | grep "physical id" | sort -u | wc -l
每个 CPU 的核数
# uniq 可以去重连续出现的相同记录
cat /proc/cpuinfo | grep "cpu cores" | uniq

make -j$(nproc)

pytest test_quick_start.py --junit-xml=report.xml
sudo sysctl -w vm.max_map_count=524240

ssh-keygen -t rsa
cat /root/.ssh/id_rsa.pub    #公钥，将以下内容加入 github 的 key

18.75分钟
export PATH=/usr/local/supa/bin/:$PATH

python3 -m pip install --upgrade pip

python3 -m pip install -e . 

narrow_down name:
sucg:TestRegressionSucg.ET_narrow_down_case
suge_it:TestRegressionIT.ET_narrow_down_case
suge_ut:TestRegressionUT.ET_narrow_down_case
sutest_client:P200/suTestNightlyNegative.negative/0
sutest_test:P200/suTestRegression.negative/0

1001 --->sucg
1002 --->suge_it
1003 --->suge_ut
1004 --->sutest_test
1007 --->br_generator

mkdir -p build && cd build && cmake .. -GNinja -DCMAKE_BUILD_TYPE=Debug -DCMAKE_EXPORT_COMPILE_COMMANDS=ON

ninja-j8
export SUPA_UDO_PATH="/home/br104/e00437/build/lib/suGE"

./sutest_client_exe --gtest_list_tests --gtest_filter=-P???/*  --gtest_output=json:result.json
```

```
1、列出所有case，耗时分布，用例按模块分组
解决方法：使用脚本实现

2、用例管理
2.1 当有大 case （耗时长）需要增加的时候，给出添加的流程
首先需要加入CI pass之后，之后添加到ET中

2.2 当出现 regression fail 的时候的处理流程
自动触发narrow down

3、用例在 sanity 和 regression 之间要做到能够快速切换
目前是手动添加

4、将 sutest case 中每个 op 在 sanity 中保留一个用例即可，其他可以移到 regression 中
根据脚本case list 手动添加


HOST_NAME='http://br-jenkins01.birentech.com:8888/'
USERNAME='jenkins'
TOKEN='11a87588fee60a04aea8abcb4de8b0f7ca'  
SULIB_JOB_NAME='Pull_Request/silicon/sulib'
SULIB_BUILD_NUMBER=2730

BR_GENERATOR_JOB_NAME='Pull_Request/br_generator'
BR_GENERATOR_BUILD_NUMBER=16875



mkdir -p build && cd build
cmake -DCMAKE_BUILD_TYPE=Release(Debug) .. && cmake --build .


cmake -DCMAKE_BUILD_TYPE=Debug .. && cmake --build .
```

```
sutest_client_exe:::P2/suTestBpa.yolov5/118 

export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
export SUTEST_PATH="${WORKSPACE}"/full-stack/test/sulib-test/sutest
export LD_LIBRARY_PATH=${HOME}/.local/lib/python3.6/site-packages/torch/lib:$LD_LIBRARY_PATH


cd ~/.local/lib/python3.6/site-packages/br_generator/tests/code_gen/ksyn_tests/workload/ut_new_arch
python3 -m pytest -m sanity  --local-tools test_softmax_opt_v2.py


sudo mount -t nfs -o vers=4,nolock,rw 
10.10.16.68:/SW/sw_mlfw/bert_data/ "$top2_training_dir"/ckpt


--json-report-file=
--junit-xml=
--gtest_output=xml:
service jenkins restart

sourc setup_sulib.sh -p silicon
source build_cmake.sh -r
```

```
--gtest_filter=P118/suTestReshapeV2.vgg16/0
b sugold_single_reshape_v2_layer.cpp:101


host     = localhost
user     = debian-sys-maint
password = x5F83hQUG4RyveYT
socket   = /var/run/mysqld/mysqld.sock
```

```
安装 br_gen_kernel
安装 br_gen
安装 sugolden
在.local文件
```

```
java:

hostname is : e00437-System-Product-Name
ip is : 127.0.1.1

python:
e00437-System-Product-Name


```

| Resource                        | Status | Labels                                                       | Ephemeral | Action           |
| :------------------------------ | :----- | :----------------------------------------------------------- | :-------- | :--------------- |
| **sw-20** *10.9.1.119*          |        | br104-pr br104-ci                                            |           | Reassign         |
| **sw-100** *10.50.32.122        |        | br104-pr br104-ci br104-ci-centos br104-ci-u20               |           | Reassign         |
| **sw-92** *10.9.0.201*          |        | br104-pr br104-ci br104-ci-centos br104-ci-u20               |           | Reassign         |
| **sw-96** *10.9.2.126*          |        | br104-pr br104-ci br104-ci-centos br104-ci-u20 br104-pr-ffmpeg |           | Reassign         |
| **sw-04** *10.9.2.115           |        | br104-pr br104-ci br104-ci-centos br104-ci-u20               |           | Unreserve        |
| **sw-63** *10.9.0.152*          |        | br104-pr br104-ci br104-ci-virtualization                    |           | Reassign         |
| **sw-81** *10.9.2.144*          |        | br104-pr br104-ci br104-ci-u20                               |           | Reassign         |
| **sw-72** *10.9.2.83*           |        | br104-pr br104-ci                                            |           | Reassign         |
| **sw-45** *10.9.2.108*          |        | br104-ci br104-ci-centos br104-ci-u20 br104-ci-15spc br104-pr br104-ci-virtualization |           | Reassign         |
| **sw-87** *10.9.0.210*          |        | br104-pr br104-ci br104-ci-centos br104-ci-14spc br104-ci-virtualization |           | Reassign         |
| **sw-78** *10.9.2.115*          |        | br104-pr_maintaining br104-ci_maintaining                    |           | Reassign         |
| **sw-82** *10.9.2.146*          |        | br104-pr br104-ci                                            |           | Reassign         |
| **supermicro-06** *10.9.2.7     |        | br104-ci-mgpu                                                |           | Reassign         |
| **sw-85** *10.9.2.148*          |        | br104-pr br104-ci                                            |           | Reassign         |
| **sw-62** *10.9.2.67*           |        | br104-pr br104-ci                                            |           | UnlockSteal lock |
| **sw-65** *10.9.2.68*           |        | br104-pr br104-ci                                            |           | UnlockSteal lock |
| **sw-64** *10.9.2.82            |        | br104-ci br104-ci-14spc br104-pr                             |           | UnlockSteal lock |
| **sw-70** *10.9.2.102*          |        | br104-pr br104-ci                                            |           | UnlockSteal lock |
| **sw-05** *10.9.0.70            |        | br104-ci br104-ci-centos br104-ci-u20 br104-ci-16spc br104-pr-ffmpeg |           | UnlockSteal lock |
| **sw-52** *10.9.0.169*          |        | br104-pr br104-ci                                            |           | UnlockSteal lock |
| **sw-71** *10.9.2.104*          |        | br104-pr br104-ci                                            |           | UnlockSteal lock |
| **build-02** *10.50.20.22       |        | build-02 cuda nested_lock02                                  |           | Reserve          |
| **cmodel-test-01** *10.10.16.32 |        | cmodel-centos cmodel-test-01                                 |           | Reserve          |
| **sw-101** *10.50.32.124*       |        | br104-pr br104-ci br104-ci-centos br104-ci-u20 br104-pr-ffmpeg |           | Reserve          |
| **sw-102** *10.50.32.121*       |        | br104-pr br104-ci br104-ci-centos br104-ci-u20               |           | Reserve          |
| **sw-13** *10.9.1.148*          |        | br104-pr br104-ci                                            |           | Reserve          |
| **sw-15** *10.9.1.181           |        | br104-ci-16spc                                               |           | Reserve          |
| **sw-16** *10.9.0.55*           |        | br104-pr br104-ci br104-ci-u20                               |           | Reserve          |
| **sw-21** *10.9.0.57*           |        | br104-pr br104-ci br104-ci-14spc br104-ci-centos br104-ci-u20 |           | Reserve          |
| **sw-23** *10.9.0.36            |        | br104-pr br104-ci                                            |           | Reserve          |
| **sw-26** *10.9.1.142*          |        | br104-pr br104-ci                                            |           | Reserve          |
| **sw-27** *10.9.1.169*          |        | br104-pr br104-ci                                            |           | Reserve          |
| **sw-33** *10.9.0.140*          |        | br104_debug_for_pr_sulib_src_dev                             |           | Reserve          |
| **sw-44** *10.9.2.74*           |        | br104-pr br104-ci                                            |           | Reserve          |
| **sw-46** *10.9.2.75            |        | br104-ci br104-ci-centos br104-ci-u20 br104-pr br104-pr-ffmpeg |           | Reserve          |
| **sw-48** *10.9.0.143           |        | br104-ci-framework                                           |           | Reserve          |
| **sw-51** *10.9.2.139           |        | br104-ci br104-ci-centos br104-ci-u20 br104-pr br104-ci-virtualization |           | Reserve          |
| **sw-54** *10.9.0.175*          |        | br104-ci br104-ci-centos br104-ci-u20 br104-pr               |           | Reserve          |
| **sw-69** *10.9.0.167           |        | br104_debug_for_pr_sulib_src_dev                             |           | Reserve          |
| **sw-73** *10.9.2.137*          |        | br104-ci br104-pr                                            |           | Reserve          |
| **sw-83** *10.9.2.153           |        | br104-pr-换硬盘 br104-ci-换硬盘                              |           | Reserve          |
| **sw-84** *10.9.2.140*          |        | br104-pr br104-ci br104-ci-centos                            |           | Reserve          |
| **sw-86** *10.9.0.206*          |        | br104-pr br104-ci br104-ci-centos br104-ci-virtualization    |           | Reserve          |
| **sw-88** *10.9.2.154*          |        | br104-pr br104-ci br104-ci-centos                            |           | Reserve          |
| **sw-89** *10.9.0.213           |        | br104-pr-maintaining br104-ci-maintaining                    |           | Reserve          |
| **sw-93** *10.9.2.125*          |        | br104-pr br104-ci br104-ci-centos                            |           | Reserve          |
| **sw-94** *10.9.0.173*          |        | br104-pr br104-ci br104-ci-centos br104-ci-u20 br104-pr-ffmpeg |           | Reserve          |
| **sw-95** *10.9.0.172*          |        | br104-pr br104-ci                                            |           | Reserve          |
| **sw-98** *10.50.32.123         |        | br104-pr br104-ci br104-ci-centos br104-ci-u20               |           | Reserve          |
| **sw-99** *10.50.32.116*        |        | br104-pr-maintaining br104-ci-maintaining br104-ci-centos-maintaining br104-ci-u20-maintaining |           |                  |

```
cat /etc/*release | grep -w PE-SYSTEM


http://br-jenkins01.birentech.com:8888/job/Pull_Request/job/silicon/job/SLO/job/br_generator/18951/ws/

pr 机器名称workspace中包含@2
88 65 20
20 65
69 70 73
73 69 64 
64 16 21 69 101

nightly 机器名称workspace中包含@2
20 88
86

python3 -m pytest -sv --tb=long --duration=0.1 --file_path=/media/regression/H.264/test_h264_1280x720.h264 /home/jenkins/workspace/BR_PACKAGE_TEST/Sdk_Silicon_Test/br104/ubuntu-18.04/BVT/br_bevc_stress/br_qa/video/tests/test_bevc_stress.py::test_bevc_stress_decode --junit-xml=/home/jenkins/workspace/BR_PACKAGE_TEST/Sdk_Silicon_Test/br104/ubuntu-18.04/BVT/br_bevc_stress/test_result/test_result_test_bevc_stress_decode_57b566f4-c5a9-3dc9-a024-e4e4c97654da.xml
```



```
1、env_set_for_jobs  get_conf_project_name  get_conf_test_type


```

```
['SoftmaxbwdTest.SoftmaxbwdAnySpcFP32', 'SoftmaxbwdTest.SoftmaxbwdAnySpcBF16']
```

```shell
export WORKSPACE=/home/jenkins/workspace/BR_PACKAGE_TEST/Sdk_Silicon_Test/br104/ubuntu-18.04/SLO/sublas
# rsync -ravzSh e00894@10.50.26.15:/home/e00894/code/br_jenkins/* ${WORKSPACE}/br_jenkins

export TIMEOUT_MINITES_CASE=10
export CASE_LIST_YAML_PATH=full-stack/test/sublas-test/config_ci.yaml

# run ci pytest step
cd "${WORKSPACE}"/br_jenkins
export PYTHONPATH=./
export LEVEL=ci_mini
export JOB_NAME=sublas
export JOB_BASE_NAME=sublas
export REPO_NAME=sublas
export ENV_FILE_PATH=full-stack/test/sublas-test/env_ci.sh
# get cases
bash "${WORKSPACE}"/br_jenkins/resources/get_case_list_interface/ci_case_list.sh -l ${LEVEL} -t rungtest
# run cases
python3 scripts/slave/nightly_test/test_common_for_all.py
```

```
11ceb12810e36981e8ff65fbf339fd2f7d
```

```
// get all env result
                sh(script: "printenv | sort > all_env_result.txt")
                archiveArtifacts("all_env_result.txt")
```

```
docker -H 10.10.64.3:4243  exec -ti $(docker -H 10.10.64.3:4243 ps | grep k8s_default_pull-request-silicon-slo-sublas-11-7cbvz-rdbcn-mmbjv | awk '{print $1}') /bin/bash
```

```
total  sulib sanity case 3706
1 core 12batch 6915
12 core gtest-parall 3120
python process poll: 12 core 3119
python process poll: 12 core 12 batch 1610
python process poll: 12 core 6 batch 1643
python process poll: 12 core 24 batch 1670
python process poll: 16 core 50 batch 1534
python process poll: 16 core 2991
python process poll: 16 core 12 batch 1581
```



```
ssh-keygen
ssh-copy-id e00437@10.50.20.170
export WORKSPACE=/home/jenkins/workspace/BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/BVT/umd
export TIMEOUT_MINITES_CASE=10
export CASE_LIST_YAML_PATH=full-stack/test/brumd-test/besu/config_ci.yaml

# run ci pytest step
cd "${WORKSPACE}"/br_jenkins
export PYTHONPATH="${WORKSPACE}"/br_jenkins
export LEVEL=regression
export JOB_NAME=br_besu
export JOB_BASE_NAME=br_besu
export REPO_NAME=br_besu
export ENV_FILE_PATH=full-stack/test/brumd-test/besu/env_ci.sh
python3 scripts/slave/nightly_test/test_common_for_all.py

rsync -ravzSh e00437@10.50.20.170:/home/e00437/vscode/br_jenkins/* ${WORKSPACE}/br_jenkins



16:28:52
16:33:35
```

gtest result

total case number：3706

| 处理方式     | 使用核数 | batch | 失败率                              | 时间(分钟)   |
| ------------ | -------- | ----- | ----------------------------------- | ------------ |
| 串行         | 1        | 1     | 0/3706                              | 300          |
| 串行         | 1        | 12    | 0/3706                              | 200          |
| gtest-parall | 12       | 1     | 5/3706                              | 48           |
| python多进程 | 12       | 1     | 7/3706 （5failure 2coredumped）     | 51.9（3119） |
| python多进程 | 12       | 12    | 39/3706 （17failure 22coredumped）  | 26.8（1610） |
| python多进程 | 12       | 24    | 2099/3706 (1failure 2098coredumped) | 27.8  (1670) |
| python多进程 | 16       | 1     | 8/3706 (5failure 3coredumped)       | 49.9（2991） |
| python多进程 | 16       | 12    | 63/3706 (17failure 46coredumped)    | 26.3（1581） |

pytest result

total case number : 71 (22 skip)

total running case number：49 （22 3 failures）

| 处理方式     | 使用核数 | batch | 失败率            | 时间(分钟)   |
| ------------ | -------- | ----- | ----------------- | ------------ |
| 串行         | 1        | 1     | 0/49              | 100          |
| python多进程 | 12       | 1     | 3/49 （3failure） | 21.8（1313） |

sulib测试线上(无rerun)

| 处理方式     | 使用核数   | batch | 失败率              | 时间(分钟)                   |
| ------------ | ---------- | ----- | ------------------- | ---------------------------- |
| python多进程 | 12 无rerun | 1     | 66/4565             | 136 (45+22+50+9+10)          |
| python多进程 | 14 rerun   | 1     | moop case失败太多了 | rerun时候文件没找到，nfs掉了 |



pr线上

| 处理方式                   | 使用核数 | batch | 失败率 | 时间(分钟) |
| -------------------------- | -------- | ----- | ------ | ---------- |
| sulib python多进程 1台机器 | 12 rerun | 1     | 0/4605 | 129        |
| sulib python多进程 3台机器 | 12 rerun | 1     | 0/4605 | 90         |
| br_gen python多进程 4 pod  | 12 rerun | 1     | 超时   |            |

```
node_ip=10.10.64.2
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_pull-request-silicon-slo-br-generator-19090-z918c-0fm0n-j1m0x | awk '{print $1}') /bin/bash


node_ip=10.10.64.2
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_pull-request-silicon-slo-br-generator-19090-z918c-0fm0n-j1m0x | awk '{print $1}') /bin/bash


node_ip=10.10.64.3
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_pull-request-silicon-slo-br-generator-only-325-9g9qk-ntl1-9sbr2 | awk '{print $1}') /bin/bash

node_ip=10.10.64.2
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_pull-request-silicon-slo-br-generator-19100-wlwr2-tx4r9-dzw6r | awk '{print $1}') /bin/bash

node_ip=10.10.64.15
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_pull-request-silicon-slo-br-generator-only-333-k9cz0-0t9h-8vrr3 | awk '{print $1}') /bin/bash

node_ip=10.10.64.11
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_ge-test-full-stack-develop-br104-ubuntu-18-04-bvt-umd-417-4rvgv | awk '{print $1}') /bin/bash

node_ip=10.10.64.13
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_kage-test-full-stack-develop-br104-ubuntu-18-04-bvt-kmd-4-t8nxc | awk '{print $1}') /bin/bash

node_ip=10.10.64.11
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_ge-test-full-stack-develop-br104-ubuntu-18-04-bvt-umd-451-dcx1 | awk '{print $1}') /bin/bash

node_ip=10.10.64.13
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_test-full-stack-rel-0630-br104-kylin-v10-slo-sulib-test-2-rs7mc | awk '{print $1}') /bin/bash
```

线上br_gen测试

| 处理方式     | 使用核数   | batch | 失败率              | 时间(分钟)                   |
| ------------ | ---------- | ----- | ------------------- | ---------------------------- |
| python多进程 | 12 无rerun | 1     | 66/4565             | 136 (45+22+50+9+10)          |
| python多进程 | 14 rerun   | 1     | moop case失败太多了 | rerun时候文件没找到，nfs掉了 |

```
SELECT 
	iID,
	rTestCaseName,
	iJobID,
	iTestSuiteID,
	rHostname,
	rName
FROM
	(
	SELECT
		(@row_number := @row_number + 1) AS num,
		Result.iID,
		Result.rTestCaseName,
		Result.iJobID,
		Result.iTestSuiteID,
		Result.rHostname,
		TestSuite.rName
	FROM
		Result
	left join TestSuite on
		TestSuite.iID = Result.iTestSuiteID,
		(SELECT@row_number := 0) AS t
	WHERE
		Result.iJobID = 20587
		AND TestSuite.rName LIKE "%.py%"
		AND TestSuite.rName LIKE "%moop_onnx%"
	ORDER BY
		Result.iID
	LIMIT 10
)temp_result
where
	num%4(total_thread) = (total_thread%treahd_id)
```

线上sulib测试 http://br-jenkins01.birentech.com:8888/job/Pull_Request/job/silicon/job/SLO/job/sulib/5792/ 2小时55分 Rel_0228/2268 1台silicon

| 处理方式           | 使用核数 | batch | 失败率 | 时间(分钟)                                                   |
| ------------------ | -------- | ----- | ------ | ------------------------------------------------------------ |
| python多进程 moop  | 12       | 1     | 26/71  | parallel: 11分25秒 11:34:50-11:46:15    rerun 43 分 29 秒 11:46:15-12:29:44 1分钟上传 |
| python多进程 sulib | 12       | 1     | 7/4595 | parallel:  81分51秒 12:30:52-13:52:43  rerun   1分26秒    13:53:16-13:54:39 13:57:22 3分钟上传 |

```
11:08:23 start  
11:34:45 run cases (26分钟半左右开始跑case)
13:57:22 end cases
14:03:46 end (6分钟半左右处理后续报告)
```

```
5041个case 2023 2 15 
```

2.16 sulib http://br-jenkins01.birentech.com:8888/job/Pull_Request/job/silicon/job/SLO/job/sulib/5911/ 2小时20分钟  develop/2336 2台silicon

| 处理方式           | 使用核数 | batch | 失败率  | 时间(分钟)                                                   |
| ------------------ | -------- | ----- | ------- | ------------------------------------------------------------ |
| python多进程 moop  | 12       | 1     | 0/71    | silicon0：6分28秒 2:06:38-2:13:06  case number:36   failed:0  rerun:0秒  上传: 0秒<br />silicon1:   2分39秒  2:08:04-2:10:43  case number:35   failed:0  rerun:0秒  上传：0秒 |
| python多进程 sulib | 12       | 1     | 16/5336 | silicon0: 84分37秒 2:13:06-3:37:43  case number:2667   failed:16  rerun 3分8秒  3:37:43-3:40:51 上传：1分32秒  3:40:51-3:42:23  <br />silicon1：81分34秒 2:10:43-3:32:17  case number:2669   failed:0  rerun 0秒  上传：50秒  3:32:17-3:33:07 |



```
1:29:18 start  
2:06:38 and 2:08:04 run cases (37-39分钟半左右开始跑case)
3:42:23 and 3:33:07  end cases  96分 and 85分
3:49:57 end (7分34秒左右处理后续报告)
```

```
5407个case 2023.2.16 
parallel number
Will pop
find failed cases

moop total time: 9分7秒
sulib total time: 171分41秒
```

2.16  br_gen http://br-jenkins01.birentech.com:8888/job/Pull_Request/job/silicon/job/SLO/job/br_generator/19409/ 2小时32分钟  develop/2336 3台silicon

| 处理方式           | 使用核数 | batch | 失败率 | 时间(分钟)                                                   |
| ------------------ | -------- | ----- | ------ | ------------------------------------------------------------ |
| python多进程 moop  | 12       | 1     | 0/71   | silicon0：6分9秒 0:40:26-0:46:35  case number:23   failed:0  rerun:0秒  上传: 0秒 <br />silicon1:   3分27秒  0:40:06-0:44:33  case number:24   failed:0  rerun:0秒  上传：0秒<br />silicon2:   1分24秒  0:40:02-0:41:26  case number:24   failed:0  rerun:0秒  上传：0秒 |
| python多进程 sulib | 12       | 1     | 1/5326 | silicon0: 49分57秒 0:46:35-1:36:32  case number:1774   failed:1  rerun 2秒   上传：1分21秒  1:36:34-1:37:53 <br />  silicon1：98分55秒 0:44:43-2:23:48  case number:1776   failed:0  rerun 0秒  上传：1分13秒  2:23:48-2:25:01 <br />silicon1：93分31秒 0:41:26-2:14:57  case number:1776   failed:0  rerun 0秒  上传：1分5秒  2:14:57-2:15:52 |

```
00:04:45 start  
2:25:01
2:37:25 end (7分34秒左右处理后续报告)

moop total time: 10分钟
sulib total time:  246分04秒
```

```
12:13
12:39 26 分开始跑case 10+16 
13:59:40
14:06
```

```
Pull_Request_silicon_SLO_br_gen
1、10.10.64.2 
2、10.10.64.6
3、10.10.64.4
4、10.10.64.2
5、10.10.64.4
6、10.10.64.4
7、10.10.64.2
8、10.10.64.4
9、10.10.64.6
10、10.10.64.2
11、10.10.64.2
12、10.10.64.4
13、10.10.64.2
14、10.10.64.4
15、10.10.64.2
16、10.10.64.2
17、10.10.64.4
18、10.10.64.6
19、10.10.64.3
20、10.10.64.6

8个：10.10.64.2
7个：10.10.64.4
4个：10.10.64.6
1个：10.10.64.3
```

br_gen

![image-20230217170655086](C:\Users\E00437\AppData\Roaming\Typora\typora-user-images\image-20230217170655086.png)

sulib

![image-20230217170711590](C:\Users\E00437\AppData\Roaming\Typora\typora-user-images\image-20230217170711590.png)

```
现阶段存在的问题：
问题一、减少job时间 
    解决方案一、100或者50个一组
    解决方案二、一个一个拿
问题二、机器监控
    机器最后扫描active，之后再扫描running case，risk是case hang和node hang

统一流程讨论的问题：
1、统一在docker中获取case list？ 待定
各自的平台：
优点：无需适配
缺点:silicon释放
docker：
优点：节省silicon资源，pipeline时间短
缺点：需要适配

warning：注意jobid
2、config_ci.yaml env_ci.sh  需要根据silicon和cmodel执行不同逻辑 
要求开发加入适配

3、每一条case是否只根据platform和case类型（pytest或gtest）可以确定
case之间没有依赖，可以确定case

4、项目下的子项目中的case是否可以交叉获取，还是必须先执行A项目的case，再执行B项目的case，还有一个情况是A适合多线程，B适合多进程
可以交叉，现阶段直接使用多进程方式

5、silicon 或者cmodel之后还有扩展该如何解决
config和env适配
```

时间统计 根据node分配case数量

| silicon0时间/分钟 | silicon1时间/分钟 | 时间/分钟 |
| ----------------- | ----------------- | --------- |
| 88                | 90                | 2         |
| 102               | 91                | 11        |
| 93                | 105               | 12        |
| 102               | 100               | 2         |
| 89                | 81                | 8         |
| 94                | 99                | 5         |
| 79                | 85                | 6         |

100 batch

| silicon0时间/分钟 | silicon1时间/分钟 | 时间/分钟 |
| ----------------- | ----------------- | --------- |
| 92                | 91                | 1         |

one by one

| silicon0时间/分钟 | silicon1时间/分钟 | 时间/分钟 |
| ----------------- | ----------------- | --------- |
| 84                | 80                | 4         |

```
线程一  指令1 指令2 指令3
线程二  指令1 指令2 指令3
parallel(线程一，线程二)
```

br_gen one by one

| node数 | parallel数 | 时间/分钟 |
| ------ | ---------- | --------- |
| 20     | 3          | 34        |
| 10     | 8          | 32        |
| 10     | 4          | 53        |
| 5      | 8          | 56        |

```
http://br-jenkins01.birentech.com:8888/job/Pull_Request/job/silicon/job/SLO/job/sulib/6087/testReport/

suEagerBatchNorm/BatchNorm.FP32Plain/0 掉卡 受影响的case suEagerBatchNorm/BatchNorm.BF16Plain/7、P3/suTestConvBiasSilu.yolov5/62
suEagerBatchNorm/BatchNorm.BF16Plain/2  掉卡
suEagerBatchNorm/BatchNorm.BF16/1 core dumped 
ReorderDryRun.Reorder_ConvAct_S8_S8 掉卡
```

```
BEBR_STATUS_SYSTEM_ERROR
/dev/biren/card_0
ErrorCode
```

```
test_inf_subgraph.py::test_bert_inf_2encoder_hbm 60分钟
test_umd_brfwd_rn50_subgraph_g14_part.py::test_rn50_subgraph_14x14_part0 40分钟
test_inf_subgraph.py::test_bert_int8_embedding_encoder_hbm 20分钟
test_umd_brbackward_losslayer_hbm_optm.py::test_brbackward_losslayer_1spc_hbm_optm 36分钟
test_umd_brbwd_subgraph_g14.py::test_rn50_bwd_subgraph_g14 75分钟

test_umd_brbackward_embedding_optimizer.py::test_BRBackward_subgraph_embedding_opt 40分钟
test_umd_brbackward_multihead.py::test_brbackward_multihead 12分钟

test_umd_brfwd_rn50_subgraph_g14_part.py::test_rn50_subgraph_14x14_part1 36分钟

test_umd_brbackward_losslayer_linear1.py::test_brbackward_losslayer_linear1 36分钟
test_umd_brbackward_losslayer_linear1_layernorm_gelu.py::test_BRBackward_losssubgraph_linear1_Layernorm_Gelu 30分钟
test_inf_subgraph.py::test_bert_int8_encoder_hbm 20分钟

test_vector_basic_op_add_for_broadcast_by_tensort_1d.py::test_vector_basic_op_add::test_low_level_basic_op_add_tensor_in_1x512x8x155_col_fp32_64x64 15分钟
test_inf_subgraph.py::test_bert_int8_2encoder_hbm 40分钟

test_brbackward_optimizer_fusedlamb_embedding.py::test_BRBackward_optimzier_embedding 50分钟
test_umd_brbwd_subgraph_g28.py::test_rn50_bwd_subgraph_g28 30 分钟
test_umd_brbackward_losslayer_hbm.py::test_brbackward_losslayer_1spc_hbm 35分钟

test_umd_brfwd_bert_subgraph_encoder0.py::test_bert_large_encoder_hbm 40分钟
test_umd_brbwd_subgraph_g7.py::test_rn50_bwd_subgraph_g7 43分钟
test_umd_brbackward_encoder_optimizer.py::test_BRBackward_subgraph_encoder_optimzier 15分钟
```

supa 

| 串行               | 并行                                                         |
| ------------------ | ------------------------------------------------------------ |
| 32分钟串行(sw-123) | 15分钟(sw-72)   203 cases rerun 2分钟 <br />4分18秒(sw-23)  0 case rerun 0秒    50分钟开始跑case |
|                    |                                                              |
|                    |                                                              |
|                    |                                                              |



sulib vs br_gen

```
定位问题： 锁定两台silicon，一个跑sulib，一个跑br_gen，登陆机器手动执行，发现sulib的cpu利用率很高，br_gen的cpu利用率太低，交换两台机器中的fullstack，发现原先跑sulib的机器还是很快，跑br_gen的机器还是很慢，排除full-stack问题。
```

```
sulib:
            yaml_file: "br_jenkins/pipeline/demo/get_case_list/gtest/config_ci_sulib.yaml"
            env_file: "br_jenkins/pipeline/demo/get_case_list/gtest/env_ci_sulib.sh"
            test_name: "sulib-test"
```

```
yaml_file
env_file  需要处理componet情况

level option 需要处理componet情况

pytest path： workspace and 需要处理componet情况
gtest option path : workspace  and 需要处理componet情况
```



| 线上测试                                                     | 单进程      | 多进程 | failed case | rerun case |
| ------------------------------------------------------------ | ----------- | ------ | ----------- | ---------- |
| br_besu 包含smoke sulib测试 （gtest、 silicon）              | 11分钟      | 5分钟  | 0个         | 0分钟      |
| br_bevc_regression 包含option （pytest、silicon）            | 1小时29分钟 | 27分钟 | 0个         | 0分钟      |
| suinfer 包含option（gtest、silicon）                         | 1分钟       | 19秒   | 0个         | 0分钟      |
| sulib 、br_gen未采用新流程，但是修改了相关共同的api，测试流程通过 |             |        |             |            |
| umd                                                          | 17分钟      | 15分钟 | 24个        | 30秒       |
| kmd                                                          | 22分钟      | 20分钟 | 28个        | 1分钟      |
| supa-op                                                      | 2小时33分钟 | 33分钟 | 7个         | 4分钟      |
| sublas                                                       | 55分钟      | 22分钟 | 0个         | 0个        |
| supa                                                         |             |        |             |            |

```
timeout 1000s /home/jenkins/workspace/BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/SLO/sulib_test/full-stack/test/sulib-test/sutest/sutest_client_exe --gtest_filter=P103/suTestConvBiasSilu.yolov5/3 --gtest_output=xml:/home/jenkins/workspace/BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/SLO/sulib_test/xmlresult/test_result9ce13361-21d5-383c-88f5-d18910bb55db.xml
```

```
timeout 1000s /home/jenkins/workspace/Merge_Request/silicon/SLO/sulib@2/full-stack/test/sulib-test/sutest/sutest_client_exe --gtest_filter=P103/suTestConvBiasSilu.yolov5/3 --gtest_output=xml:/home/jenkins/workspace/Merge_Request/silicon/SLO/sulib@2/xmlresult/test_result9ce13361-21d5-383c-88f5-d18910bb55db.xml
```

```
timeout 1000s /home/jenkins/workspace/Merge_Request/silicon/SLO/sulib@2/full-stack/test/sulib-test/sutest/sutest_client_exe --gtest_filter=P103/suTestConvBiasSilu.yolov5/39 --gtest_output=xml:/home/jenkins/workspace/Merge_Request/silicon/SLO/sulib@2/xmlresult/test_result34721fbe-a604-395f-aaa6-2452a34a0054.xml
```

```
sudo mount -t nfs -o vers=4,ro 10.10.16.68:/SW/sw_infra/jenkins ./ 
```

supa-op重名suite

| TACOTRON2 | Tacotron2 |
| --------- | --------- |
| FACENET   | FaceNet   |
| CONFORMER | Conformer |
| YOLOV3    | Yolov3    |
| RN50      | rn50      |
| YOLO      | Yolo      |
| EAGER     | Eager     |
| DirectOP  | DirectOp  |
| BERT      | Bert      |
| PerfOP    | PerfOp    |
| RETINANET | Retinanet |
| Apex      | Apex      |

llvm-project重名suite name

| TACOTRON2 | Tacotron2 |
| --------- | --------- |
| FACENET   | FaceNet   |
| CONFORMER | Conformer |
| EAGER     | Eager     |



大小写冲突

```
Mask_rcnn.
  NMS
Mask_RCNN.
  RoIAlignAllBF16

CONFORMER.
	SupaMatrix2DChunkFP32Chunk2Dim0
	SupaMatrix2DChunkFP32Chunk3Dim0
Conformer.
	testOpSupaArangeGFP32

 23792
```



```
Apex
Conformer
Mask_rcnn
Retinanet
FACENET
Corformer
```

```
ALTER TABLE TestSuite MODIFY COLUMN rName VARCHAR(256) BINARY

SELECT * FROM TestSuite WHERE rName = '/home/jenkins/workspace/Merge_Request/silicon/SCP/supa-op/full-stack/test/supa-op-test/supaopTest:::Apex'

INSERT INTO TestSuite (rName)VALUES('/home/jenkins/workspace/Merge_Request/silicon/SCP/supa-op/full-stack/test/supa-op-test/supaopTest:::apex')
```

```
job_id生成方式需要改变
```

```
if filter_info.count("--gtest_filter=") > 1:
   filter_info = filter_info.replace("--gtest_filter=", ":")
   filter_info = filter_info.replace(":", "--gtest_filter=", 1)
```

```
296987
```

```
TestAPIThreads.single_16parallel
find TestAPIThreads.single_16parallel belong to test_brml
test_result991d4cb7-7279-39ad-82b8-c5939498f248.xml

TestAPIThreads.single_16parallel
find TestAPIThreads.single_16parallel belong to ml_test
test_resultd715864f-81ad-3b7e-8a7d-56e10813124d.xml
```

```
一、收集case list 
python3 -m pip install pytest-json-report
python3 -m pytest ./ --co --continue-on-collection-errors --json-report --json-report-file=case_list.json

二、执行脚本
python3 run_pytest_cases.py

```

```
ENV LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8
```

```
node_ip=10.10.64.11
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_k-develop-br104-ubuntu-18-04-bvt-virtualization-common-24-7ntbm | awk '{print $1}') /bin/bash

node_ip=10.10.64.12
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_merge-request-silicon-bvt-br-besu-775-zz2fl-nv2fc-pkrmp | awk '{print $1}') /bin/bash

node_ip=10.10.64.5
docker -H $node_ip:4243  exec -ti $(docker -H $node_ip:4243 ps | grep k8s_default_ge-test-full-stack-develop-br104-ubuntu-18-04-bvt-umd-469-xq45d | awk '{print $1}') /bin/bash
```

```
sql = """INSERT INTO Result
                            (rTestCaseName, rResult, rReason, yComparable, yBiggerIsBetter,
                            fValue, rUnit, sStartTime, sEndTime, iDurationMS,
                            rComment, iJobID, iTestSuiteID)
                            VALUES(%s, %s, %s, %s, %s,
                                   %s, %s, %s, %s, %s,
                                   %s, %s, %s)"""
```

```
SELECT Result.iID, Result.rTestCaseName, Result.sStartTime, (unix_timestamp(Result.sEndTime) - unix_timestamp(Result.sStartTime)) as duration  ,Result.iJobID, Result.iTestSuiteID, Result.rHostname, TestSuite.rName
                            FROM Result
                            left join TestSuite on TestSuite.iID = Result.iTestSuiteID
                            WHERE Result.iJobID = 24942
                            AND TestSuite.rName LIKE "%.py%"
                            AND TestSuite.rName not LIKE "%moop_onnx%"
                            AND Result.rStatus != "active"
                            ORDER BY duration ASC ,Result.sStartTime DESC 
```

br_gen超时分析

```
一次性拿10个case导致某一个pod获取时间最长的case，导致时间极度不平衡
```







数据库时间花费（suBLAS 分析）分析的suBLAS数据采用单进程

```json
quick dict:  {'get_suite_id()': 1.3313796520233154, 'get_case_type()': 1.239776611328125e-05, 'duration_cases_insert()': 2.076630115509033, 'create_a_job_with_test_cases()': 2.2767794132232666, 'count_standard_all_cases()': 0.33393311500549316, 'retrieve_standard_active_cases()': 161.90877604484558, 'end_test_case()': 1384.2705965042114}
slow dict:  {'get_suite_id()': 2.070985794067383, 'get_case_type()': 1.239776611328125e-05, 'duration_cases_insert()': 2.8377115726470947, 'create_a_job_with_test_cases()': 3.8273425102233887, 'count_standard_all_cases()': 4.111810684204102, 'retrieve_standard_active_cases()': 1094.3791937828064, 'end_test_case()': 11295.97157049179}
sublas120 dict:  {'get_suite_id()': 1.2697172164916992, 'get_case_type()': 1.4066696166992188e-05, 'duration_cases_insert()': 2.202211380004883, 'create_a_job_with_test_cases()': 2.4552407264709473, 'count_standard_all_cases()': 0.07915592193603516, 'retrieve_standard_active_cases()': 81.94923639297485, 'end_test_case()': 33.944496154785156, 'get_not_finished_status_cases()': 0.02432394027709961}
```

|                              | get_suite_id() | get_case_type() | duration_cases_insert() | create_a_job_with_test_cases() |
| ---------------------------- | -------------- | --------------- | ----------------------- | ------------------------------ |
| suBLAS quick （job正常并发） | 1.33秒         | 0.01 秒         | 2.08秒                  | 2.23秒                         |
| suBLAS slow （job并发多）    | 2.07秒         | 0.01秒          | 2.84秒                  | 3.83秒                         |
| suBLAS fixed （job正常并发） | 1.27秒         | 0.01秒          | 2.20秒                  | 2.46秒                         |

|                              | count_standard_all_cases() | retrieve_standard_active_cases() | end_test_case()           |
| ---------------------------- | -------------------------- | -------------------------------- | ------------------------- |
| suBLAS quick（job正常并发）  | 0.33秒                     | 161.91秒 （单进程时间）          | 1384.27秒 （单进程时间）  |
| suBLAS slow（job并发多）     | 4.11秒                     | 1094.37秒 （单进程时间）         | 11295.97秒 （单进程时间） |
| suBLAS fixed （job正常并发） | 0.08秒                     | 81.95秒  （多进程时间）          | 33.95秒 （多进程时间）    |

```
问题：retrieve_standard_active_cases() end_test_case() 耗费时间太长
原因：数据库需要锁表，retrieve_standard_active_cases()和end_test_case()多次访问，造成时间太长，其他函数也需要锁表但是次数很少，占用时间非常少，
解决方案：
步骤一、将update（end_test_case）类似的函数去除表锁
步骤二、将跑完之后结果，一次性更新到数据库中，减少数据库访问（之前是一个一个update）
步骤三、获取case一次性获取多个（默认是100个），非标准例如br_gen一个一个拿case，因为一旦一次性拿100个，由于br_gen是分布式多机器的，某一台机器会拿到最长的100个case，导致时间极度不平衡，解决方式是调整数据库获取方式，每间隔 cases总数/获取数量 条拿一条case，拿够一百条case
```



br_gen目前采用多进程

```json
br_gen_nofix dict:  {'get_suite_id()': 36.1661536693573, 'get_case_type()': 0.00048065185546875, 'duration_cases_insert()': 37.332069396972656, 'create_a_job_with_test_cases()': 38.538097620010376, 'count_all_cases()': 197.66183495521545, 'retrieve_active_cases()': 119824.73451542854, 'end_test_case()': 120931.82639551163}

br_gen_fixed dict:  {'get_suite_id()': 39.89870238304138, 'get_case_type()': 0.0005106925964355469, 'duration_cases_insert()': 41.07966613769531, 'create_a_job_with_test_cases()': 47.12724685668945, 'count_all_cases()': 2.750218629837036, 'retrieve_active_cases()': 3820.9862263202667, 'end_test_case()': 138.54332375526428}
```

|                           | get_suite_id() | get_case_type() | duration_cases_insert() | create_a_job_with_test_cases() |
| ------------------------- | -------------- | --------------- | ----------------------- | ------------------------------ |
| br_gen_nofix（正常并发）  | 36.17秒        | 0.01秒          | 37.33秒                 | 38.54秒                        |
| br_gen_fixed （正常并发） | 39.90秒        | 0.01秒          | 41.08秒                 | 47.13秒                        |

|                           | count_all_cases() | retrieve_active_cases()                      | end_test_case()                              |
| ------------------------- | ----------------- | -------------------------------------------- | -------------------------------------------- |
| br_gen_nofix（正常并发）  | 197.66秒          | 119824.73秒 （多进程并发时间，不是串行时间） | 120931.82秒 （多进程并发时间，不是串行时间） |
| br_gen_fixed （正常并发） | 2.75秒            | 3820.99秒 （多进程并发时间，不是串行时间）   | 138.54秒 （多进程并发时间，不是串行时间）    |

```
一、收集case list 
binary_name --gtest_list_tests  --gtest_output=json:./gtest_case_list.json

二、执行脚本
将脚本中 binary_name_dir 改为自己二进制文件路径
python3 run_gtest_cases.py
```

```
case_path: /home/jenkins/workspace/Merge_Request/silicon/SLO/supa-op/full-stack/test/supa-op-test/supaopTest
case_type: rungtest
test_suite: YOLOV5
case_name: bpw1d_fp32
case_option: no
repo: sulib
case_timeout: 5
case_duration: 10


 

case_path: /home/jenkins/workspace/Merge_Request/silicon/SLO/supa-op/full-stack/test/pytest_example.py
case_type: runpytest
test_suite:  ClassName
case_name: test_example
case_option: no
repo: sulib
case_timeout: 5
case_duration: 10
```

```
error case is ASSTRIDEDSuite.AsStrided_Buffer2Matrix3D_UINO_FP32
error case is WAVEGLOW.DirectOpConv1x1ActSplitBF16
error case is TACOTRON2.ComplexMergeFP32
error case is RngXowowUniform.RngXowowUniformWeightUma
error case is ASSTRIDEDSuite.AsStrided_Buffer2Activation_UINO_FP32
error case is RngXowowUniform.RngXowowUniformVectorUma
```

```python
def get_case_list_set_up():
    workspace = get_workspace_env()
    job_base_name = get_job_base_name_env()
    # parse yaml
    config_yaml_path = f"{workspace}/infra-config/test/unstandard_test/unstandard_test.yaml"
    config_yaml_data = read_yaml(config_yaml_path)
    env_file_value = config_yaml_data["yaml_path_for_repo"][job_base_name]["env_file"]
    # check stack package component, （yaml_file、env_file）need to replace
    stack_package_component = get_stack_package_component_env()
    if stack_package_component:
        env_file_value = env_file_value.replace(
            "full-stack", stack_package_component
        )
    test_name_value = config_yaml_data["yaml_path_for_repo"][job_base_name]["test_name"]

    # set repo name and env_file_path
    os.environ["REPO_NAME"] = job_base_name
    os.environ["ENV_FILE_PATH"] = env_file_value

    # process full stack test pkg
    process_full_stack_test_pkg_cmd = f"bash -c 'source {workspace}/br_jenkins/scripts/slave/common_sh/common_full_stack_test.sh && process_full_stack_test_pkg {test_name_value}'"
    retcode, _ = run_standard_shell_cmd(process_full_stack_test_pkg_cmd)
    handle_cmd_return(retcode, process_full_stack_test_pkg_cmd)

    # common_setup
    common_setup_cmd = f"bash -c 'source {workspace}/br_jenkins/scripts/slave/common_sh/common_full_stack_test.sh && common_setup'"
    retcode, _ = run_standard_shell_cmd(common_setup_cmd)
    handle_cmd_return(retcode, common_setup_cmd)

    generate_case_list_and_write_to_db(case_type="all",yaml_path=config_yaml_path)
```

```
http://infra.biren.com:32080/v1/tests/queue/out_queue/BR_PACKAGE_TEST_umd/211_ubuntu-18.04

http://infra.biren.com:32080/v1/tests/queue/out_queue/BR_PACKAGE_TEST_umd/197_ubuntu-18.04?number=1

http://infra.biren.com:32080/v1/tests/queue/left_case/BR_PACKAGE_TEST_umd/226_ubuntu-18.04
```

```
状态码： 404 队列被删除 信息："this pipeline info is not found"
状态码： 302 队列empty case 信息： "not found"
```

```
fast:
error case is P117/suTestResampleFwdV2.Bert/5
error case is P1/suTestADD.bert/80
error case is P20/suTestTypeCast.typecast/50
error case is P103/suTestConvBiasSilu.yolov5/62
```

```
/home/jenkins/workspace/Merge_Request/silicon/FPA/br_pytorch/full-stack/test/torch_br-test/test/test_argmax.py
```

```
SELECT * FROM Job j  WHERE j.rName = "BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/FPA/br_pytorch" AND j.iBuildNumber = 347
319483

test.test_flip （suite)
test_flip[dtype0-input_shape25-dim_list25] (case_name) 全等匹配==》suite in result

{"job_name":"BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/FPA/br_pytorch","build_num_bad":347,"regression_case":"test.test_flip.test_flip[dtype0-input_shape25-dim_list25]","os":"ubuntu-18.04"
```

```
NODE_LABELS
```

```
25.231
3642.427
```

```
2347 upload local xml cases to nfs
2348 running cmd: timeout
```

```
job id from db is:33464
```

```
runpytest test/test_KLDivloss.py TestKLDivloss test.test_KLDivloss.TestKLDivloss
```

```
runpytest test/test_models/test_dbnet.py TestDBNet
```

```
1、export WORKSPACE=/home/jenkins/workspace/BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/FPA/br_tensorflow
   export PLATFORM=br104
   export JOB_BASE_NAME=br_tensorflow
   export ENV_FILE_PATH=/home/jenkins/workspace/BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/FPA/br_tensorflow/full-stack/test/tensorflow-test/env_ci.sh

2、bash -c "source /home/jenkins/workspace/BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/FPA/br_tensorflow/br_jenkins/scripts/slave/common_sh/common_full_stack_test.sh && common_setup"

3、 bash -c "source /home/jenkins/workspace/BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/FPA/br_tensorflow/br_jenkins/scripts/slave/case_standard/../common_sh/common_full_stack_test.sh && common_full_stack_test_envset && env"
4、timeout 10m python3 -m pytest -sv --tb=long   /home/jenkins/workspace/BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/FPA/br_tensorflow/full-stack/test/tensorflow-test/test/test_tf.py::CwiseTest::test_cwise_mul_broadcast -o junit_family=xunit1 --junit-xml=broadcase.xml


source /home/jenkins/workspace/BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/FPA/br_tensorflow/full-
stack/test/tensorflow-test/env_ci.sh && setup
bash -c "source /home/jenkins/workspace/BR_PACKAGE_TEST/Full_Stack/develop/br104/ubuntu-18.04/FPA/br_tensorflow/br_jenkins/scripts/slave/case_standard/../common_sh/common_full_stack_test.sh && common_full_stack_test_envset && env"
```

```
export PLATFORM=br104
export WORKSPACE=/home/jenkins/workspace/BR_PACKAGE_TEST/AI_SDK/Rel_0630/br104/ubuntu-18.04/SLO/sulib_test
export CASE_LIST=all
export LEVEL=regression
source env_ci.sh && env_set
bash -c "source run_case_main.sh  && main full-stack ${LEVEL}"

source /etc/profile.d/biren.sh
stackpath="${WORKSPACE}"/"${STACK_PACKAGE_COMPONENT:-full-stack}"
IS_SDK_PKG=yes
if [ -n "$IS_SDK_PKG" ] && [ "$IS_SDK_PKG" = 'yes' ]; then
pushd "${stackpath}/test/sulib-test/"
export LD_LIBRARY_PATH=$PWD/sutest
popd
fi
```

```
LD_LIBRARY_PATH=/home/jenkins/workspace/BR_PACKAGE_TEST/AI_SDK/Rel_0630/br104/ubuntu-18.04/SLO/sulib_test/full-stack/test/sulib-test/sutest
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
LC_MEASUREMENT=C.UTF-8
SSH_CONNECTION=10.50.20.170 42734 10.50.32.228 22
LESSCLOSE=/usr/bin/lesspipe %s %s
LC_PAPER=C.UTF-8
LC_MONETARY=C.UTF-8
LANG=C.UTF-8
OLDPWD=/home/jenkins/workspace/BR_PACKAGE_TEST/AI_SDK/Rel_0630/br104/ubuntu-18.04/SLO/sulib_test/full-stack/test/sulib-test
CASE_LIST=all
LC_NAME=C.UTF-8
XDG_SESSION_ID=264
USER=jenkins
LEVEL=regression
WORKSPACE=/home/jenkins/workspace/BR_PACKAGE_TEST/AI_SDK/Rel_0630/br104/ubuntu-18.04/SLO/sulib_test
PWD=/home/jenkins/workspace/BR_PACKAGE_TEST/AI_SDK/Rel_0630/br104/ubuntu-18.04/SLO/sulib_test/full-stack/test/sulib-test/ci
HOME=/home/jenkins
SSH_CLIENT=10.50.20.170 42734 22
XDG_DATA_DIRS=/usr/local/share:/usr/share:/var/lib/snapd/desktop
LC_ADDRESS=C.UTF-8
LC_NUMERIC=C.UTF-8
SSH_TTY=/dev/pts/1
MAIL=/var/mail/jenkins
TERM=xterm
SHELL=/bin/bash
PLATFORM=br104
SHLVL=1
PYTHONPATH=/usr/lib:/usr/lib64:/opt/biren/tensor-engine/python/tvm/python:/opt/biren/tensor-engine/python:/usr/lib:/usr/lib64:/opt/biren/tensor-engine/python/tvm/python:/opt/biren/tensor-engine/python:
LC_TELEPHONE=C.UTF-8
LOGNAME=jenkins
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
XDG_RUNTIME_DIR=/run/user/1000
PATH=/opt/biren/suvs/bin:/usr/local/supa/bin:/opt/biren/bin:/opt/biren/cmodel-server/bin:/opt/biren/biren-tools:/home/jenkins/.local/bin:/opt/biren/suvs/bin:/usr/local/supa/bin:/opt/biren/bin:/opt/biren/cmodel-server/bin:/opt/biren/biren-tools:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
LC_IDENTIFICATION=C.UTF-8
LESSOPEN=| /usr/bin/lesspipe %s
LC_TIME=C.UTF-8
_=/usr/bin/env

```

```
test_tf.py MmaTest test_tf.MmaTest
```

```
Merge_Request/silicon/SLO/suBLAS
http://10.9.0.54:8888/job/Merge_Request/job/silicon/job/SLO/job/suBLAS/
145
29735
```

```
suEagerBatchNorm/BatchNormBwd.NUMAActPlainBF16/9
suEagerBatchNorm/BatchNormActivationBwd.UMAActPlainBF16/17
suEagerBatchNorm/BatchNormBwd.UMABF16/17
suEagerBatchNorm/BatchNormBwd.UMAActPlainFP32/3
```

```
<SPC> hang!

gtest:
    level:
      ci_mini:
      	test_brtorch: --gtest_filter=ExceptionTest.TestCheckInternalException
      sanity:
        test_brtorch: --gtest_filter=*
      regression:
        test_brtorch: --gtest_filter=*
      full:
        test_brtorch: --gtest_filter=*
      stress:
        test_brtorch: --gtest_filter=*
```

```
2,011次失败 http://br-jenkins01.birentech.com:8888/job/Merge_Request/job/silicon/job/SLO/job/sulib/7862/testReport/
```

```
case_path: /home/jenkins/workspace/Merge_Request/silicon/SLO/supa-op/full-stack/test/supa-op-test/supaopTest
case_type: rungtest
test_suite: YOLOV5
case_name: bpw1d_fp32
case_option: ""
repo: sulib
case_timeout: 5
case_duration: ""
case_entrypoint: ""

case_time: ""
case_result: ""
job_name: Merge_Request/silicon/SLO/supa-op
job_base_name: supa-op
build_number: 12
node_name: ""
case_message: ""




 

case_path: /home/jenkins/workspace/Merge_Request/silicon/SLO/supa-op/full-stack/test/pytest_example.py
case_type: runpytest
test_suite:  ""
case_name: test_example
case_option: ""
repo: sulib
case_timeout: 5
case_duration: ""
case_entrypoint: ""

case_time: 1.0
case_result: ""
job_name: Merge_Request/silicon/SLO/supa-op
job_base_name: supa-op
build_number: 12
node_name: ""
case_message:""

```

```
SoftMaxFwdTest.SoftMaxFwdFP32NUMA
GridSample.nameTestOpGridSampleBackward
```

```
http://infra.biren.com:32080/v1/tests/queue/in_queue
http://infra.biren.com:32080/v1/tests/queue/left_case
http://infra.biren.com:32080/v1/tests/queue/case/status
http://infra.biren.com:32080/v1/tests/queue/out_queue

http://infra.biren.com:32080/v1/api/jenkins/standard_test

http://10.50.25.226:11110/api/common/jenkins/update
http://10.50.25.226:11110/api/common/jenkins
http://10.50.25.226:11110/api/common/jenkins &job_name={job_name}&job_base_name={job_base_name}
```

```
1、潜在风险：环境会映射到系统
```

```
{
    "case_name": "bpw1d_fp32",
    "case_result": "SUCCESS",
    "build_number": 12,
    "case_time": 18.0,
    "job_base_name": "supa-op",
    "job_name": "Merge_Request/silicon/SLO/supa-op",
    "case_type": "rungtest",
    "test_suite": "YOLOV5",
    "case_message": "unit test"
 }
```

```
[ {
        "case_path": "/home/jenkins/workspace/Merge_Request/silicon/SLO/supa-op/full-stack/test/supa-op-test/supaopTest",
        "case_timeout": "20",
        "case_name": "bpw1d_fp32",
        "test_suite": "YOLOV5",
        "repo": "sulib",
        "case_type": "rungtest",
    },
    {
        "case_path": "/home/jenkins/workspace/Merge_Request/silicon/SLO/supa-op/full-stack/test/pytest_example.py",
        "case_timeout": "30",
        "case_name": "test_example",
        "test_suite": "",
        "repo": "sulib",
        "case_type": "runpytest",
    }
]
```

```
1、打包功能shi'xian
2、
```

```
http://infra.biren.com:32080/v2/api/jenkins/standard_test?name=supa&jobType=mr
```

```
 1，整体框架梳理 100%  
 2，外部模块依赖整合，封装打包工具实现 100% 
 3, 脚本参数解析，配置文件解析设计 100% （ci online）
 4，case list整合入run cases stage 30%
```

```
1、一个dict语法错误 dict遍历忘记写items()
2、一个拼写错误 proccessnum
3、一个地址逻辑错误 为赋值就是用 case_list_dir
4、一个参数位置错误 dmesg_log赋值位置错误
AssertionError: Post case list url http://infra.biren.com:32080/v1/tests/queue/in_queue/Merge_Request_br_besu/70_ubuntu-18.04_br104-ci error, error code is 403, error message is data has already exsits
5、queue需要加入repo标识，相应的报告处理需要加入
6、返回数据不是json格式
7、'Connection aborted.', BrokenPipeError(32, 'Broken pipe') urllib版本需要>=1.26
```

```
1.	分支合并策略制定 Yueye
2.	python post大文件处理  Yueye & Qiwen ETA：7/12
3.	repo依赖敲定 Gang
4.	单包编译存在pkg_svc找不到问题的修复 Chengcheng ETA：7/12
5.	服务监控状态显示 Zhiming
6.	mongo业务的优先级和价值 Zhiming
7.	上传下载数据集通过web方式 Tianwei
8.	优化服务提供页面 Gang
```

```
common_full_stack_test_envset repo_env_set
common_setup repo_common_setup 
common_teardown repo_common_teardown
set_common_env common_repo_env_set 
owner_repo_env_set common__env_set 
```

```
{     "id": 5027,     "glmrbGhRepository": "Arch/sgpt",     "glmrbActualCommit": "8b561cbaf24fb4841b4f8b290b2fc83a97210dd2",     "glmrbActualCommitAuthorEmail": "bgu@birentech.com",     "glmrbPullId": 42,     "glmrbPullDescription": "",     "glmrbPullLongDescription": null,     "glmrbPullTitle": "add build id to version",     "glmrbPullLink": "https://gitlab.birentech.com/Arch/sgpt/-/merge_requests/42",     "glmrbSourceBranch": "ci_test",     "glmrbTargetBranch": "develop",     "glmrbCommentBody": "@jenkins",     "sha1": "origin/pr/42/merge",     "repoBaseName": "sgpt",     "glmrbAuthorRepoGitUrl": "ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git",     "glmrbTriggerAuthorEmail": "lilyli@birentech.com",     "glmrbPullAuthorEmail": "lilyli@birentech.com",     "glmrbTriggerAuthorName": "lily(\u987e\u5f6c)",     "glmrbPullAuthorLogin": "E01293",     "jobBaseName": "sgpt",     "glmrbSourceRepoName": "Arch/sgpt",     "glmrbSourceHttpUrl": "https://gitlab.birentech.com/Arch/sgpt.git",     "glmrbTargetHttpUrl": "https://gitlab.birentech.com/Arch/sgpt.git",     "glmrbSourceSshUrl": "ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git",     "glmrbTargetSshUrl": "ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git",     "LABEL": "br104-ci-mgpu",     "STACK_PACKAGE_COMPONENT": "full-stack",     "STACK_PACKAGE_BRANCH": "develop",     "STACK_PACKAGE_VERSION": "latest",     "USE_BR_PACKAGE": "yes",     "restoreSystem": "Yes",     "OS": "ubuntu-18.04",     "ENABEL_COMMON_TEST": "yes",     "LEVEL": "sanity",     "DEPS_CODE": "NO",     "LIB_VERSION": "",     "PR_BRANCH": "develop",     "IS_DEBUG": "no",     "EMAIL_RECIPIENT": "sw.qe.infra@birentech.com",     "mrInfoMap": "",     "SILICON_PROCESS_NUM": "1",     "TIMEOUT_MINITES_JOB": "400",     "TIMEOUT_MINITES_NODE": "350",     "TIMEOUT_MINITES_CASE": "30" }
```

```
br_pytorch_weekly
```

```
{     "id": 5027,     "glmrbGhRepository": "Arch/sgpt",     "glmrbActualCommit": "8b561cbaf24fb4841b4f8b290b2fc83a97210dd2",     "glmrbActualCommitAuthorEmail": "bgu@birentech.com",     "glmrbPullId": 42,     "glmrbPullDescription": "",     "glmrbPullLongDescription": null,     "glmrbPullTitle": "add build id to version",     "glmrbPullLink": "https://gitlab.birentech.com/Arch/sgpt/-/merge_requests/42",     "glmrbSourceBranch": "ci_test",     "glmrbTargetBranch": "develop",     "glmrbCommentBody": "@jenkins",     "sha1": "origin/pr/42/merge",     "repoBaseName": "sgpt",     "glmrbAuthorRepoGitUrl": "ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git",     "glmrbTriggerAuthorEmail": "lilyli@birentech.com",     "glmrbPullAuthorEmail": "lilyli@birentech.com",     "glmrbTriggerAuthorName": "lily(\u987e\u5f6c)",     "glmrbPullAuthorLogin": "E01293",     "jobBaseName": "sgpt",     "glmrbSourceRepoName": "Arch/sgpt",     "glmrbSourceHttpUrl": "https://gitlab.birentech.com/Arch/sgpt.git",     "glmrbTargetHttpUrl": "https://gitlab.birentech.com/Arch/sgpt.git",     "glmrbSourceSshUrl": "ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git",     "glmrbTargetSshUrl": "ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git",     "LABEL": "br104-ci-mgpu",     "STACK_PACKAGE_COMPONENT": "full-stack",     "STACK_PACKAGE_BRANCH": "develop",     "STACK_PACKAGE_VERSION": "latest",     "USE_BR_PACKAGE": "yes",     "restoreSystem": "Yes",     "OS": "ubuntu-18.04",     "ENABEL_COMMON_TEST": "yes",     "LEVEL": "sanity",     "DEPS_CODE": "NO",     "LIB_VERSION": "",     "PR_BRANCH": "develop",     "IS_DEBUG": "no",     "EMAIL_RECIPIENT": "sw.qe.infra@birentech.com",     "mrInfoMap": "",     "SILICON_PROCESS_NUM": "1",     "TIMEOUT_MINITES_JOB": "400",     "TIMEOUT_MINITES_NODE": "350",     "TIMEOUT_MINITES_CASE": "30" }
```

```
ssh://git@gitlab.birentech.com:2222/software/br_ci_lib.git
```

```
JFROG="http://br-artifactory.birentech.com:8082/artifactory"
wget -Nq "${JFROG}"/br_public/ci_deps/sulibs/ubuntu1804_python38/torch-1.10.0a0+git71f889c-cp38-cp38-linux_x86_64.whl
python3 -m pip install torch-1.10.0a0+git71f889c-cp38-cp38-linux_x86_64.whl --force-reinstall
```

```
scp e00437@10.50.20.170:/home/e00437/vscode/br_jenkins/docker/jenkins-slave/jenkins_agent_config_for_server.sh .
scp e00437@10.50.20.170:/home/e00437/vscode/br_jenkins/ansible/playbooks/templates/jdk_upgrade.sh .
```

```
{"id": 35169, "glmrbGhRepository": "Arch/sgpt", "glmrbActualCommit": "68986af96fc7df018671fbe41fe6c1b44f0ab0b5", "glmrbActualCommitAuthorEmail": "hdong@birentech.com", "glmrbPullId": 130, "glmrbPullDescription": "", "glmrbPullLongDescription": null, "glmrbPullTitle": "wget -q", "glmrbPullLink": "https://gitlab.birentech.com/Arch/sgpt/-/merge_requests/130", "glmrbSourceBranch": "wget_q", "glmrbTargetBranch": "develop", "glmrbCommentBody": "@jenkins", "sha1": "origin/pr/130/merge", "repoBaseName": "sgpt", "glmrbAuthorRepoGitUrl": "ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git", "glmrbTriggerAuthorEmail": "hdong@birentech.com", "glmrbPullAuthorEmail": "hdong@birentech.com", "glmrbTriggerAuthorName": "Hao Dong(\u5f20\u6d69\u4e1c)", "glmrbPullAuthorLogin": "E01047", "jobBaseName": "sgpt", "glmrbSourceRepoName": "Arch/sgpt", "glmrbSourceHttpUrl": "https://gitlab.birentech.com/Arch/sgpt.git", "glmrbTargetHttpUrl": "https://gitlab.birentech.com/Arch/sgpt.git", "glmrbSourceSshUrl": "ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git", "glmrbTargetSshUrl": "ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git", "MAIL": "", "LEVEL": "", "LIB_VERSION": "", "mrInfoMap": "{     \"id\": 5027,     \"glmrbGhRepository\": \"Arch/sgpt\",     \"glmrbActualCommit\": \"8b561cbaf24fb4841b4f8b290b2fc83a97210dd2\",     \"glmrbActualCommitAuthorEmail\": \"bgu@birentech.com\",     \"glmrbPullId\": 42,     \"glmrbPullDescription\": \"\",     \"glmrbPullLongDescription\": null,     \"glmrbPullTitle\": \"add build id to version\",     \"glmrbPullLink\": \"https://gitlab.birentech.com/Arch/sgpt/-/merge_requests/42\",     \"glmrbSourceBranch\": \"ci_test\",     \"glmrbTargetBranch\": \"develop\",     \"glmrbCommentBody\": \"@jenkins\",     \"sha1\": \"origin/pr/42/merge\",     \"repoBaseName\": \"sgpt\",     \"glmrbAuthorRepoGitUrl\": \"ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git\",     \"glmrbTriggerAuthorEmail\": \"lilyli@birentech.com\",     \"glmrbPullAuthorEmail\": \"lilyli@birentech.com\",     \"glmrbTriggerAuthorName\": \"lily(\\u987e\\u5f6c)\",     \"glmrbPullAuthorLogin\": \"E01293\",     \"jobBaseName\": \"sgpt\",     \"glmrbSourceRepoName\": \"Arch/sgpt\",     \"glmrbSourceHttpUrl\": \"https://gitlab.birentech.com/Arch/sgpt.git\",     \"glmrbTargetHttpUrl\": \"https://gitlab.birentech.com/Arch/sgpt.git\",     \"glmrbSourceSshUrl\": \"ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git\",     \"glmrbTargetSshUrl\": \"ssh://git@gitlab.birentech.com:2222/Arch/sgpt.git\",     \"LABEL\": \"br104-ci-mgpu\",     \"STACK_PACKAGE_COMPONENT\": \"full-stack\",     \"STACK_PACKAGE_BRANCH\": \"develop\",     \"STACK_PACKAGE_VERSION\": \"latest\",     \"USE_BR_PACKAGE\": \"yes\",     \"restoreSystem\": \"Yes\",     \"OS\": \"ubuntu-18.04\",     \"ENABEL_COMMON_TEST\": \"yes\",     \"LEVEL\": \"sanity\",     \"DEPS_CODE\": \"NO\",     \"LIB_VERSION\": \"\",     \"PR_BRANCH\": \"develop\",     \"IS_DEBUG\": \"no\",     \"EMAIL_RECIPIENT\": \"sw.qe.infra@birentech.com\",     \"mrInfoMap\": \"\",     \"SILICON_PROCESS_NUM\": \"1\",     \"TIMEOUT_MINITES_JOB\": \"400\",     \"TIMEOUT_MINITES_NODE\": \"350\",     \"TIMEOUT_MINITES_CASE\": \"30\" }", "CONFIG_VERSION": "", "CONFIG_BRANCH": "", "SDK_TYPE": "internal", "BR_JENKINS_BRANCH": ""}
```

```
sgpt调试总结：
1、full-satck包有问题
2、开发特殊需求，开发脚本中需要获取level，但是获取level是在设置开发环境之前，需要将level设置在开发脚本之前 INFRA-9144
3、开发不需要安装kmd
4、submoudule没有设置
5、日志被关闭，无法查看信息
6、查找二进制文件失败，已修复 INFRA-9116
7、开发环境没有sudo权限
8、tmux启动失败，开发修复
9、开发机器在使用，导致跑的结果没用
10、开发误关闭service
11、conda安装失败
```

```
10.49.5.118
curl -sO http://10.9.0.54:8888/jnlpJars/agent.jar
java -jar agent.jar -jnlpUrl http://10.9.0.54:8888/computer/br104%2Dsgpt%2D8%2Dcards/jenkins-agent.jnlp -workDir "/home/jenkins"
```

```
get_repo_yaml_config_data
59 23 * * 1,2,3,4,7 
```

```
PYTORCH_VERSION
MOUNT_KERNEL_CACHE
BRTB_RUN_IN_SINGLE_MODE
CASE_ERROR_EXIT
```

```
Test Suite,Total,Pass,Failure,Time Out,Unknown,Pass Rate
TOTAL,2767,2147,0,1,619,77.59%
Test_Suite,Total, Pass,Time_Out, Failure, Unknown, Pass_Rate,
```

```
f" {workspace}/".join(pytest_path_job_base_name)
```

```
{"id": 20226, "glmrbGhRepository": "software/br_bebr", "glmrbActualCommit": "2d49482b687dcbb9f8c943f451b728fe51d2094f", "glmrbActualCommitAuthorEmail": "justuswang@birentech.com", "glmrbPullId": 2486, "glmrbPullDescription": "ci_config_start\n\n### OS ###\n- [x] ubuntu-18.04\n***\n### LEVEL ###\n- [ ] ci_mini\n- [x] sanity\n- [ ] regression\n***\n### ENV ###\n#### CMODEL ####\n- [ ] BR104\n- [ ] BR110\n#### SILICON ####\n- [x] BR104\n- [ ] BR110\n***\n### CHANGE IMPACT ###\n#### Interface change ####\n##### compilation #####\n- [ ] br_becl\n- [ ] br_besu\n- [ ] br_behi\n- [ ] br_bevc\n##### functionality #####\n- [ ] umd(bebr/besu/beki/becl)\n- [ ] kmd\n- [ ] supa\n- [ ] llvm-project\n- [ ] tensor-engine\n- [ ] br_bevc\n- [ ] suAutostream\n- [ ] br_ml\n- [ ] suvs\n- [ ] suInfer\n- [ ] br_pytorch\n- [ ] br_tensorflow\n- [ ] sulib\n- [ ] moop_onnx\n- [ ] gstreamer\n- [ ] BrJpegDec\n***\n#### Test Case ####\n- [ ] Unit test\n- [x] CI Test\n- [ ] QA Test\n***\n- [ ] Documentation update\n\nci_config_end", "glmrbPullLongDescription": null, "glmrbPullTitle": "test: ci", "glmrbPullLink": "https://gitlab.birentech.com/software/br_bebr/-/merge_requests/2486", "glmrbSourceBranch": "empty_pr", "glmrbTargetBranch": "develop", "glmrbCommentBody": "@jenkins", "sha1": "origin/pr/2486/merge", "repoBaseName": "br_bebr", "glmrbAuthorRepoGitUrl": "ssh://git@gitlab.birentech.com:2222/software/br_bebr.git", "glmrbTriggerAuthorEmail": "sunnywang@birentech.com", "glmrbPullAuthorEmail": "justuswang@birentech.com", "glmrbTriggerAuthorName": "Sunny Wang(\u738b\u4e9a\u8339)", "glmrbPullAuthorLogin": "E00014", "jobBaseName": "br_bebr", "glmrbSourceRepoName": "software/br_bebr", "glmrbSourceHttpUrl": "https://gitlab.birentech.com/software/br_bebr.git", "glmrbTargetHttpUrl": "https://gitlab.birentech.com/software/br_bebr.git", "glmrbSourceSshUrl": "ssh://git@gitlab.birentech.com:2222/software/br_bebr.git", "glmrbTargetSshUrl": "ssh://git@gitlab.birentech.com:2222/software/br_bebr.git", "LABEL": "br104-ci", "STACK_PACKAGE_COMPONENT": "full-stack", "STACK_PACKAGE_BRANCH": "develop", "STACK_PACKAGE_VERSION": "lkg", "PKG_TYPE": "deb", "USE_BR_PACKAGE": "yes", "CG_WHL_BRANCH": "develop", "CG_WHL_VERSION": "latest", "TIMEOUT_MINITES_JOB": "270", "restoreSystem": "Yes", "OS": "ubuntu-18.04", "TIMEOUT_MINITES_NODE": "240", "BUILD_TYPE": "Release", "ENABEL_COMMON_TEST": "yes", "EMAIL_RECIPIENT": "sw.qe.infra@birentech.com", "mrInfoMap": "", "LIB_VERSION": "", "LEVEL": "", "SILICON_PROCESS_NUM": "1"}
```

```
87071291
87071292
```

```
upload local xml cases to nfs 4778
result upload success 4778
total case number is
```

sgpt结果失败统计

| 时间                                   | 失败原因                                                     | 原因归结 |
| -------------------------------------- | ------------------------------------------------------------ | -------- |
| 8.7-8.8                                | 开发机器未restore，jnlp_service未配置，导致为无法自动连接    | CI       |
| 8.9-8.10                               | 如期运行，结果有误                                           | 开发     |
| 8.11-8.13                              | 开发机器占用                                                 | 开发     |
| 8.14-8.15 （118机器占用，配置111机器） | 配置111机器                                                  | CI/开发  |
| 8.16                                   | 掉卡，full-stack合入导致，ci清除kmd版本                      | 开发     |
| 8.17 （成功）                          |                                                              |          |
| 8.21-8.22                              | 加入python新包，开发机器未restore，ansible未安装相应的包，timeout时间设定问题 | CI       |
| 8.23 （成功）                          |                                                              |          |

推送 docker file镜像

```
执行 build.sh  /home/e00437/vscode/br_jenkins/docker/jenkins-slave/master
sudo tmpreaper -a --verbose=0 --showdeleted --mtime-dir "${hour2keep}" --protect 'Full_Stack/develop/br104/ubuntu-18.04/FPA/br_pytorch_weekly{/*,/*/*,/*/*/*,/*/*/*/*}' "${parent_dir}"/"${the_dir}"
```



```
# add case_time, get case time from mongodb
# test_suite = case_dict["test_suite"]
# case_name = case_dict["case_name"]

# get_case_time_params = {
#     "job_name": job_name,
#     "job_base_name": job_base_name,
#     "build_number": build_number - 1,
#     "test_suite": test_suite,
#     "case_name": case_name,
# }
# get_case_time_url = (
#     mongodb_url_class.GET_POST_RESULT_URL
#     + "?"
#     + urlencode(get_case_time_params)
# )
# response_data = get_response_data(get_case_time_url)
# response_data_content = json.loads(response_data.content)
# if response_data_content["code"] == 0:
#     case_time = float(response_data_content["data"]["case_time"])
#     break

repo_total_case_list.sort(key=lambda k: (k.get("case_time", 3600.0)), reverse=True)
```

```
http://10.9.0.54:8888/job/Merge_Request/job/silicon/job/ARCH/job/sgpt/112/consoleFull
http://10.9.0.54:8888/job/Merge_Request/job/silicon/job/SLO/job/sulib/6915/
```

```
test_op_div.py::test_div_regression[case_cfg0-0]
test_op_div.py::test_div_regression[case_cfg1-1]
```

```
collected 102341 items / 52455 deselected / 49886 selected
44762
5124
```

```
cmd_info = "cmd_info:" + " ".join(cmds)
    rerun_message = "rerun_message:" + rerun_message
    host_name = "hostname:" + socket.gethostname()
    host_ip = "hostip:" + extract_ip()
    host_name_ip = host_name + " " + host_ip
    current_time = "current_time:" + str(datetime.now())

```

```
pprint(f"{repo_name} gtest get case list cmd is {gtest_case_list_cmd}")
# check rerun status
if rerun_status:
    rerun_message = "already rerun!!!"
else:
    rerun_message = "norerun"
```

