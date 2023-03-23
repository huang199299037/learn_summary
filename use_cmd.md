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

option 需要处理componet情况
```

