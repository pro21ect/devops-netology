1. git rev-parse aefea
Ответ
 ХЭШ          aefead2207ef7e2aa5dc81a34aedf0cad4c32545
Запрос
git show aefead2207ef7e2aa5dc81a34aedf0cad4c32545
Ответ
комметарий     Update CHANGELOG.md

Можно сразу git show aefea

2. git show 85024d3
Коммит 85024d3100126de36331c6982bfaac02cdab9e76
tag: v0.12.23
 как вариант
git tag --points-at 85024d3

3. git log --no-walk b8d720^@
или тоже самое git show b8d720^1
	       git show b8d720^1	

	2 родителя
9ea88f22fc6269854151c571162c5bcf958bee2b
56cd7859e05c36c06b56d013b55a252d0bb7e158

4. git log --pretty=oneline v0.12.23...v0.12.24
33ff1c03bb960b332be3af2e333462dde88b279e (tag: v0.12.24) v0.12.24
b14b74c4939dcab573326f4e3ee2a62e23e12f89 [Website] vmc provider links
3f235065b9347a758efadc92295b540ee0a5e26e Update CHANGELOG.md
6ae64e247b332925b872447e9ce869657281c2bf registry: Fix panic when server is unreachable
5c619ca1baf2e21a155fcdb4c264cc9e24a2a353 website: Remove links to the getting started guide's old location
06275647e2b53d97d4f0a19a0fec11f6d69820b5 Update CHANGELOG.md
d5f9411f5108260320064349b757f55c09bc4b80 command: Fix bug when using terraform login on Windows
4b6d06cc5dcb78af637bbb19c198faff37a066ed Update CHANGELOG.md
dd01a35078f040ca984cdd349f18d0b67e486c35 Update CHANGELOG.md
225466bc3e5f35baa5d07197bbc079345b77525e Cleanup after v0.12.23 release

5.git log -S"func providerSource"
Изменения происходят в 2-х хэшах. Оставил оба, т.к функцию по сути переписали.
5af1e6234ab6da412fb8637393c5a17a1b293663
8c928e835

6.git log -L :globalPluginDirs:plugins.go --oneline
41ab0aef7
52dbf9483
78b122055

7.Martin Atkins