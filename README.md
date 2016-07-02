# Azure 기술 문서 참여자 가이드

Azure 설명서 센터에 게시된 기술 문서에 대한 원본을 저장하는 GitHub 리포지토리는 [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation)에서 찾습니다.

또한 이 리포지토리는 기술 설명서에 참가하는 데 도움이 되는 지침을 포함합니다. 참여자의 가이드에서 문서 목록은 [인덱스](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md)를 참조하세요.

## Azure 문서에 참가

Azure 문서에 관심을 가져 주셔서 감사합니다.

* [참가 방법](#참가-방법)
* [Azure 콘텐츠에 참가 방법](#azure-콘텐츠에-참가-방법)
* [리포지토리 조직](#리포지토리-조직)
* [GitHub, Git 및 이 리포지토리 사용](#github-git-및-이-리포지토리-사용)
* [항목의 서식을 지정하는 마크다운을 사용하는 방법](#항목의-서식을-지정하는-마크다운을-사용하는-방법)
* [추가 리소스](#추가-리소스)
* [모든 참여자 가이드 문서의 인덱스](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md)(새 페이지를 엽니다)

## 참가 방법

GitHub 사용자 인터페이스에서 기술 문서에 쉽게 참여할 수 있습니다. 이 레포지토리에서 문서를 찾거나 [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation)의 문서를 방문하여 문서에 대한 GitHub 원본으로 이동하는 문서의 링크를 클릭합니다.

##Azure 콘텐츠에 참가 방법

###사소한 수정

이 레포지토리의 설명서 및 코드 예제에 대해 제출한 사소한 수정 또는 확인된 내용은 [Azure 웹 사이트 사용 약관(ToU)](http://azure.microsoft.com/support/legal/website-terms-of-use/)에서 다룹니다.


###대규모 제출

설명서 및 코드 예제에 새롭거나 중요한 변경 내용을 통해 끌어오기 요청을 제출하면 다음과 같은 그룹에 있는 경우 온라인 참가 사용권 계약(CLA)을 제출하라고 요청하는 GitHub의 주석을 보냅니다.

* Microsoft Open Technologies 그룹의 멤버입니다.
* Microsoft 직원이 아닌 참가자입니다.

끌어오기 요청을 수락할 수 있기 전에 온라인 양식을 완료해야 합니다.

전체 세부 사항은 [http://azure.github.io/guidelines.html#cla](http://azure.github.io/guidelines.html#cla)에서 확인할 수 있습니다.

## 리포지토리 조직

azure-content 리포지토리의 내용은 [Azure.Microsoft.com](http://azure.microsoft.com)에 있는 설명서의 구성을 따릅니다. 이 리포지토리에는 두 개의 루트 폴더가 포함됩니다.

### \articles

*\articles* 폴더는 *.md* 확장을 사용하여 마크다운 파일 형식의 설명서를 포함합니다.

루트 디렉터리의 문서는 경로 *http://azure.microsoft.com/documentation/articles/{article-name-without-md}/*의 Azure.Microsoft.com에 게시됩니다.

* **문서 파일 이름:** [파일 명명 지침](https://github.com/Azure/azure-content/blob/master/contributor-guide/file-names-and-locations.md)을 참조하세요.

고유한 서비스 폴더 내의 문서는 경로 
*http://azure.microsoft.com/documentation/articles/service-folder/{article-name-without-md}/*의 Azure.Microsoft.com에 게시됩니다

* **미디어 하위 폴더:** *\articles* 폴더는 루트 디렉터리 문서 미디어 파일에 대한 *\media* 폴더를 포함하며 이 안에는 각 아티클에 대한 이미지가 담긴 하위 폴더가 있습니다. 서비스 폴더는 각 서비스 폴더 내에서 문서에 대한 별도의 미디어 폴더를 포함합니다. 문서 이미지 폴더는 *.md* 파일 확장을 뺀 문서 파일과 이름이 동일합니다.

### \includes

하나 이상의 문서에 포함될 재사용 가능한 콘텐츠 섹션을 만들 수 있습니다. [기술 콘텐츠에 사용되는 사용자 지정 확장](https://github.com/Azure/azure-content/blob/master/contributor-guide/custom-markdown-extensions.md)을 참조하세요.

### \markdown templates

이 폴더는 문서에 필요한 기본 마크다운 서식을 사용하는 표준 마크다운 템플릿이 포함됩니다.

### \contributor-guide

이 폴더는 참여자의 가이드 중 일부인 문서를 포함합니다.

## GitHub, Git 및 이 리포지토리 사용

참가하는 방법, 몇 가지 변경에 참가하는 GitHub UI를 사용하는 방법 및 중요한 참가에 대한 리포지토리를 분기 및 복제하는 방법에 대한 내용은 [GitHub에서 작성을 위한 도구 설치 및 설정](https://github.com/Azure/azure-content/blob/master/contributor-guide/tools-and-setup.md)을 참조하세요.

GitBash를 설치하고 로컬에서 작동하도록 선택하면 새 로컬 작업 분기를 만들고 내용을 변경하며 주요 분기에 변경 내용을 다시 제출하는 단계는 [새 문서를 만들거나 기존 문서를 업데이트하기 위한 Git 명령](https://github.com/Azure/azure-content/blob/master/contributor-guide/git-commands-for-master.md)에 나열됩니다.

### 분기

변경의 특정 범위를 대상으로 하는 로컬 작업 분기를 만드는 것이 좋습니다. 각 분기는 모두 단일 개념/문서로 제한하여 워크플로를 간소화하고 병합 충돌의 가능성을 줄여야 합니다. 다음은 새 분기에 대한 적절한 범위입니다.

* 새 문서(및 연결된 이미지)
* 문서의 맞춤법 및 문법 편집입니다.
* 많은 문서 집합에 단일 서식 변경 내용을 적용합니다.(예: 새 저작권 바닥글)

## 항목의 서식을 지정하는 마크다운을 사용하는 방법

이 레포지토리의 모든 문서는 GitHub 버전의 마크다운을 사용합니다. 다음은 리소스 목록입니다.

- [마크다운 기본 사항](https://help.github.com/articles/markdown-basics/)

- [인쇄 가능한 마크다운 치트 시트](https://github.com/Azure/azure-content/blob/master/contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- 마크다운 편집기의 목록은 [도구 및 설치 항목](https://github.com/Azure/azure-content/blob/master/contributor-guide/tools-and-setup.md#install-a-markdown-editor)을 참조하세요.

## 문서 메타데이터

문서 메타데이터를 사용하면 azure.microsoft.com 웹 사이트에서 작성자 특성, 참여자 특성, 이동 경로, 문서 설명 및 SEO 최적화 뿐만 아니라 Microsoft가 콘텐츠의 성능을 평가하는 데 사용하는 보고 등과 같은 특정 기능을 사용할 수 있습니다. 따라서 메타데이터는 중요합니다! [메타데이터가 제대로 수행되도록 하기 위한 지침은 다음과 같습니다](https://github.com/Azure/azure-content/blob/master/contributor-guide/article-metadata.md).

## 추가 리소스

모든 지침 항목은 [참가자 가이드의 인덱스](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md)를 참조하세요.

<!----HONumber=AcomDC_0307_2016-->
