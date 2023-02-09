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
export LEVEL=sanity
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
