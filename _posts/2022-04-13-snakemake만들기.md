---
layout: post
title: "snakemake"
date: 2022-04-13 00:00:00 +0900
header:
    overlay_color: "#000"
    overlay_filter: "0.5"
    overlay_image: https://duc-ke.github.io/testio/assets/images/posts/TinyIsland_game.png
    teaser: https://duc-ke.github.io/testio/assets/images/posts/TinyIsland_game.png
  
excerpt: "머신러닝 지도학습 기초(1)"

categories: 
- Development
- machineLearning
tag: 
- ML
---
# snakemake(파이프라인을 만들자)

Created Time: May 10, 2019 11:56 PM
Last Edited: November 4, 2019 8:41 AM
Status: Completed
Tags: BI, IT
파일 업로드: https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/snakemake_190511.pptx, https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/snakemake_advanced_190511.pptx

# Snakemake의 출범

![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled.png)

# Snakemake 기막힌 특징.

- Python 기반의 구문이라 보면 읽힘.
    - 단, Snakemake 문법을 따로 공부할 필요
- Shell 명령 사용가능
- Python 명령 사용가능
- 샘플 분석 병렬화 가능 (threads opt 지원)
    
    
    - ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%201.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%201.png)
    
- 위 처럼 작업흐름도 생성(DAG)
    
    
    ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%202.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%202.png)
    
- Cluster 환경 지원(qsub.. 다만 뭔갈 설정해야하나.. 아직 시도 못해봄)
- Log파일을 정리
- Conda와 연계 가능
- 기타 등등..

사실 다 뜯어보고 나면, Snakemake가 엄청 기막히다 라고 할만한 특징은 없을 수도 있더라.

그래서 내가 써본 결과를 요약하면,

1. 만드는데 개고생..(snakemake문법이 익숙치 않아서.. 오래걸리고 디다.)
2. 쓰는 사람은 엄청 편함
3. 만들고 나면 다른 언어로 파이프라인 만든 것보다 이쁨 (..?🐒)
4. 파이프라인을 구조화 시켜 쓸수 있는 장점. 
    
    ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%203.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%203.png)
    
    - 개발자나 expert BI 코딩한 사람이 아니면 그렇게 별 장점으로 안느껴짐
    - 프로그래밍을 해보면 사람마다 코딩 스타일이 천차만별이라 의식의 흐름대로 1회성 파이프라인 만드는 사람은  다른사람이 아예 알아볼 수가 없는데, snakemake로 구조화 시켜놓으면 이를 쓰는 사람도, 공동 업그레이드 할 사람도 알아 볼 수 있음
5. Server의 resource를 최대한으로 쉽게 이용할 수 있다.
    - 즉, Server 자원 낭비를 줄인다.
    - 즉, Server의 구석구석의 자원들(cpu, mem, cluster)이 쉬는 꼴을 못보게 알 수 있다.
    - 즉, 효율적인 분석이 가능하다.
    

# 고롬, 문법을 공부해 보자.

자세한 문법은 튜토리얼을 봐야 지대로 이해가 되.(어느 순간..)

여기선, 몇가지 예제를 보면서 snakemake 문법으로 된 코딩을 어떻게 snakemake 컴파일러가 해석을 하는지에 대해 배워보고 일부 코딩 스타일을 확인해 보는 시간을 가져 보도록 해보자.

~~(Trust me. 이 방법이 젤 빨리 터득하는 방법임.)~~

### Basic 문법 스터디 :

[https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/snakemake_190511.pdf](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/snakemake_190511.pdf)

### Snakemake 문법으로 파이프라인을 만들다보면 아래의 QnA 들이 도움이 될거야

Q? `all rule의 input:` 에 명시하는 파일들은 무엇을 의미?

> 생성되야 하는 파일들을 의미. 해당 파일들이 나올때까지 관련 룰들을 연결
> 

Q? all말고 다른 rule의 `input:`  에 파일을 명시해주면? 

> 해당 파일이 현시점에 존재하는지 검사하고 없다면 프로그램 종료
> 

Q? 모든 rule이 샘플 병렬로 일어나는지?

> 의존성 없는 작업은 병렬로, 샘플이 합쳐지는 룰은 먼저 분석된 샘플은 대기.
> 

Q? 분석을 수행하다 오류가 발견되어 재분석을 하려한다. 그럼 중간 생성파일도 새로 덮어쓰는가?

> 아니다, 이미 생성된 파일이 있으면 그 다음 룰부터 시행이되므로, 재시작을 원하는 프로세스는 그에 해당하는 중간 생성파일들을 지우고 돌려야 한다.
> 

Q? 파이프라인을 만들었으면 매번 모든(처음부터 끝) 프로세스를 돌려야 하는건가?

> 각각의 개별 rule을 강제로 실행하게 할 수있다.
> 

### 주요 Command lines

```python
# 예비 실행(dry-run) : 문법체크
snakemake –n

# shell 실행 커맨드 프린트
snakemake –p

# print excusion reason for each job
snakemake –r

# run rule all(Snakefile 이란 제목의 파일이 실행 위치에 있어야 함)
snakemake

# 특정 snakefile 지정
snakemake -snakefile aaa.snakefile
```

### Advanced Command lines

```python
# 특정 rule 실행
snakemake rule1.out.txt

# visualization of DAG
snakemake –dag | dot –Tsvg > dag.svg

# 최대 8코어 사용해서 병렬 run
snakemake --cores 8

# conda config 사용할 경우
snakemake --use-conda
```

### Advanced 문법 스터디:

[https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/snakemake_advanced_190511.pdf](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/snakemake_advanced_190511.pdf)

## 참고로 vim, vi 사용하여  snakefile 코딩시 문법 syntax 스타일링 할 수 있더라.

- 우리 notion의 읽을 포스팅에서 [snakefile syntax file for vim](https://www.notion.so/Snakefile-syntax-file-for-vi-vim-16898c54a55446dd876ce317525811bb) 참고.

## Snakemake 를 이용하여 fastq 부터 vcf까지

tutorial을 진행하며 간단한 variant calling pipeline을 만들어 본다. (conda는 이미 설치되어 있다고 보고 진행)

- 준비
    - 예제 파일 다운 로드
    
    ```bash
    wget [https://github.com/snakemake/snakemake-tutorial-data/archive/v5.4.5.tar.gz](https://github.com/snakemake/snakemake-tutorial-data/archive/v5.4.5.tar.gz)
    tar -xf v5.2.3.tar.gz --strip 1
    ```
    
    - conda 가상환경 생성 및 필요 프로그램 설치
    
    ```bash
    conda env create --name snakemake-tutorial --file environment.yaml
    conda activate snakemake-tutorial
    ```
    
    ![environment.yaml 파일안에는..](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%204.png)
    
    environment.yaml 파일안에는..
    
- mapping & calling review
    - 우리는 이미 'Conda 하자(feat. NGS mapping)' 포스팅 에서 shell 창에 한줄 한줄 명령어를 통해 mapping과 calling을 경험 했었다. (아래 링크 참고)
    
    [Conda 하자.(feat. NGS mapping)](https://www.notion.so/Conda-feat-NGS-mapping-775d71a13ea2464c87b6e3d505b18bca)
    
    - 기억이 나지 않는 사람을 위해 간단한 리뷰
    
    ```bash
    # a, b, c, d, e 폴더 임의 지정
    # 가상환경  ('bit_알파벳' 이름으로)
    conda create -n bit_a
    source activate bit_a
    
    # 폴더 구조 확인하기
    
    # 파이프라인 위한 프로그램 설치하기
    conda install samtools picard bwa bcftools
    
    # 두 프로그램 정상 동작 확인하기
    samtools
    picard
    bwa
    
    # reference 폴더 이동하여 인덱스 파일 
    cd reference 
    samtools faidx dog.chr38.fa  # .fai 생성
    picard CreateSequenceDictionary R=dog.chr38.fa # .dict 생성
    bwa index -a bwtsw dog.chr38.fa # .amb, .ann, bwt, .pac, .sa 생성
    cd ..
    
    # mapping하기
    bwa mem reference/dog.chr38.fa sample/dog1_1.fastq.gz sample/dog1_2.fastq.gz | samtools view -Sb - > dog1.bam
    
    # sorting하기
    samtools sort -T dog1 -O bam dog1.bam > dog1.sorted.bam
    
    # sorted.bam indexing하기
    samtools index dog1.sorted.bam
    
    # variant calling하기
    samtools mpileup -g -f reference/dog.chr38.fa dog1.sorted.bam | bcftools call -mv - > all.vcf
    ```
    
    - 위의 파이프라인과 똑같은 플로우를 따르는 snakemake 파이프라인을 만들 것이다.
- mapping snakemake 작성
    - snake파일을 만든다.
    
    ```bash
    vi Snakefile
    ```
    
    - mapping rule 작성.
        
        ![rule의 기본 형태](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%205.png)
        
        rule의 기본 형태
        
    - 해당 룰의 input파일과 생성 파일을 적고 실행 커맨드를 명시한다
    
    ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%206.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%206.png)
    
    - shell에서 변수로 바꿀 수 있는 부분을 와일드 카드로교체 한다.
    
    ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%207.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%207.png)
    
    - 실행한다.
    
    ```bash
    snakemake
    혹은,
    snakemake -p
    ```
    
- mapping snakemake 업그레이드
    - `snakemake-tutorial-data-5.4.5/data/samples` 에 모든 샘플에 대한 rule을 작성하고 싶다면, wildcard `{변수명}` 을 이용한다.
    
    ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%208.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Untitled%208.png)
    
    - 이렇게 만들면 어떤 샘플을 돌릴지 모르므로 아래 처럼 최종 생성 파일을 지정해 줘야 한다.
    
    ```bash
    # A만 돌리고 싶다면
    snakemake -p mapping/A.bam
    
    # A, B, C 돌리고 싶다면
    snakemake -p mapping/A.bam mapping/B.bam mapping/C.bam
    ```
    
    - 위에 코드는 다음과 같은 상황에 잘 쓰이게 된다 :  snakefile안에 룰이 여러 개일 때 특정 룰만 돌리고 싶을 때,  특정 샘플만 돌리고 싶을 때, 생성파일을 지칭해준다.
    
    ### 여기서, snakemake의 꿀팁(장점)
    
    - 분석 실행 로그를 보면 샘플의 순서가 어떻게 진행 되는지 알 수 있다.(여기선 C → B → A 차례대로 연이어 분석이 실행 된다.
    
    ![aaa.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/a_2019_11_01__12_55_29.png)
    
    - 그러나, 본인의 컴터가 좋다면 thread 기능을 이용하여 CPU 각 core당 한 샘플씩 맡길 수 있다. (`--cores` 옵션). 이렇게 되면 3샘플이 모두 병렬 처리되어 체감상 더 빠르단 걸 알 수 있을 정도다.
    
    ```bash
    snakemake -p mapping/A.bam mapping/B.bam mapping/C.bam --cores 3
    ```
    
- sorting snakemake 작성
    - mapping을 했으니 bam을 sorting 하는 rule을 만들어 본다. 마찬가지, 형태부터 잡는다.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.06.07.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.06.07.png)
        
    - 이번 input이 뭔지 생각해보고 채운다.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.07.44.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.07.44.png)
        
    - samtools로 sorting하는 코드는 길다. 길땐 요렇게 쓰면 이쁘다.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.09.48.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.09.48.png)
        
    - 완성.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.33.57.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.33.57.png)
        
    - snakemake를 실행시켜 테스트 해본다 (에러가 날것이다)
        
        ```bash
        snakemake -p sorting/A.sort.bam
        ```
        
    - 이유는 shell 구문에선 현재 rule밖의 wildcard에 진입이 불가능 하기 때문이다. 문법을 조금 고쳐 주면 된다.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.36.21.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.36.21.png)
        
    - 이제 다시 실행 시켜보자. 오해 하지 말아야 하는건 mapping, sorting 파일이 없어도 위의 실행 구문은 sorting만 하는 것이 아니라 mapping, sorting 두 rule이 함께 작동하게 되어 파이프라인화 된다는 것이다.
    
- index snakemake 작성
    - calling 전엔 bam의 index파일을 생성 해야 한다. input과 output이 뭔지 곰곰이 생각하며 룰을 추가 해본다.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.41.38.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.41.38.png)
        
- calling snakemake 작성
    - calling 작성은 생각을 이전보다 많이 요구 한다. 집중해야 한다.
    - 먼저 필요 풀 스크립트를 확인해 보자.
    
    ```bash
    # A만 calling하고 싶다면?
    samtools mpileup -g -f data/genome.fa sorting/A.sort.bam | bcftools call -mv - > calling/all.vcf
    
    # 두 개체 이상을 함께 calling하고 싶다면?
    samtools mpileup -g -f data/genome.fa sorting/A.sort.bam sorting/B.sort.bam sorting/C.sort.bam | bcftools call -mv - > calling/all.vcf
    ```
    
    - 위의 스크립트를 rule로 만들어 보자. 아마 input부터 막힐 것이나 그런 부분은 물음표(??)로 적어 두고 채울 수 있는 곳부터 채워 보자.
        - 아래처럼 채웠으면 아주 잘 따라 오고 있다는 것이다.
            
            ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.56.17.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__1.56.17.png)
            
    - 우선 input과 output에는 하위 변수를 설정하여 파일마다 변수에 할당 할수 있다. 여러개인 경우 쉼표(,)를 꼭! 적어 줘야 한다. 변수 할당시 '.' 을 이용하여 변수에 접근 가능하다.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__2.01.35.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__2.01.35.png)
        
    - 위의 코드는 A, B까지만 동작하며 A, B, C에 대해선 동작하지 않을 것이다. 이럴경우 snakemake에서 제공하는 expand 함수를 이용해야 한다.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__2.07.42.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__2.07.42.png)
        
    - expand 함수는 내부적으로 map()함수와 비슷하게 작동하며, 실행이 끝나면 아래 처럼 리스트를 만들어 준다고 생각하면 된다.
    
    ```bash
    ["sorting/A.sort.bam", "sorting/B.sort.bam", "sorting/C.sort.bam"]
    ```
    
    - 그럼 실행하여 테스트 해보자. 어떤 코드를 써야 실행될까?
        
        ```bash
        snakemake -p call/all.vcf
        ```
        
    
    ### 여기서 snakemake 꿀팁2.
    
    - snakemake로 파이프라인을 만들면 전체 파이프 라인을 DAG(Directed acyclic graph) 그림으로 그려준다.
    - 그리는 코드는 아래와 같다.
    
    ```bash
    snakemake --dag call/all.vcf | dot -Tsvg > dag.svg
    ```
    
    - 생성된 svg는 브라우저로 확인할 수 있다.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.06.43.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.06.43.png)
        
    
    - 사실 마지막 코드가 실행이 되긴 하겠지만, 우리가 DAG에서 보듯, 만들어 놓은 rule하나가 실행이 되지 않고 있다.
        - 바로 indexing rule. index파일은 calling에는 필수가 아니였을 지여도 보통 calling pipeline에선 꼭 필요하다. 없으면 뒷단의 프로세스가 모두 에러를 토한다.
    - snakemake에서는 input과 output이 서로 연결되어야 파이프라인으로 rule들이 이어진 다는 것을 눈치 챘을 텐데, 그렇다면 index는 어디에 위치 시켜야 할까?
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.13.50.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.13.50.png)
        
    - 완성 된 모양을 DAG로 확인해 보자.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.14.38.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.14.38.png)
        
    
- 타겟 rule 작성
    - 지금까지는 실행 명령어에 최종 파일을 적어 실행하였지만 내부에 숨기는 것이 일반적이다.
    - 아래와 같이 스크립트 맨위에 rule all을 코딩 해보자.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.26.28.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.26.28.png)
        
    - snakemake에는 `all` 이라는 rule에 적힌 파일 목록들을 기점으로 모든 rule을 탐색하여 파이프라인 순서를 구성하게 된다.
    - 이젠 아래 처럼 편히 실행하면 된다.
    
    ```bash
    snakemake -p --cores 5
    ```
    
- 만들어진 전체 파이프라인을 보면 다음과 같다.
    
    ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.30.18.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.30.18.png)
    
- 다양한 기능들이 있는데 하나만 더 써보자
    - bio tool들은 thread를 고려해서 만드는것 들이 많다.
    - bwa  툴이 그렇다.  mapping 룰에서 thread를 지원가능하다.  -t
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.52.18.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.52.18.png)
        
    - snakemake에선 요렇게 하지 말고 아래 처럼 예약어를 이용하여 수정하는 것이  좋다.
        
        ![https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.54.55.png](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/_2019-11-01__3.54.55.png)
        
    
- 완성본 업로드 :
    
    [Snakefile](https://duc-ke.github.io/testio/assets/images/posts/a2022_04_13_snakemake/Snakefile.txt)
    
    1[1월3일 분석 폴더 링크](https://www.dropbox.com/sh/djegf586x4z19is/AACFScNHiQDytq8nEcBxy9oIa?dl=0)
    

## GATK Best Practice를 따라만든 snakemake pipeline

github & bitbucket 에 snakemake 파이프라인을 올려놨음.

아직 부족한 상태이기 때문에 BIT와 함께 조금씩 뜯으며 완성해 보려 함.

[https://github.com/duc-ke/gatk3bestpractice](https://github.com/duc-ke/gatk3bestpractice)

## reference

---

- PyCon Korea 2015, 탐색적으로 큰 데이터 분석하기 발표
- Snakemake Tutorial ppt
- [https://snakemake.readthedocs.io/en/stable/tutorial/setup.html](https://snakemake.readthedocs.io/en/stable/tutorial/setup.html)