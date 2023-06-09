# ZADANIE 2
Zadanie 2 z Technologi Chmurowych

## CZĘŚĆ OBOWIĄZKOWA
<br/><hr/><br/>
a. zmodyfikować kod źródłowy tak by aplikacja wyświetlała imię oraz nazwisko studenta<br/>
![image](https://github.com/voiteckheir/zadanie2/assets/91530837/7e7c4ae4-f18d-4047-bffc-99e1d0963682)
<br/>


<br/><hr/><br/>
b. opracować łańcuch działań w ramach Github Actions, który pozwoli na zbudowaniu
obrazów Docker zgodnych z OCI dla dwóch architektur sprzętowych: x86_64 oraz arm64
(procesor M1/M2).

łańcuch jest uruchamiany na podstawie pusha do repozytorium


```
      - name: Build and push ghcr
        uses: docker/build-push-action@v2
        id: build
        with:
          platforms: linux/x86_64,linux/arm64/v8
          context: .
          file: ./Dockerfile
```

<br/><hr/><br/>
c. obraz ma zostać poddany testowi pod kątem CVE z wykorzystaniem dowolnego (jednego
z trzech) narzędzi przedstawionych na końcu instrukcji do laboratorium nr 12. Obraz nie
może mieć żadnych zagrożeń krytycznych.

Do sprawdzenia obrazu użyłem narzędzia SNYK. <br/>
został dodany token (SNYK_TOKEN) w celu używania tego narzędzia.<br/>
została ustawiona flaga która wyświetli tylko błędy krytyczne
![image](https://github.com/voiteckheir/zadanie2/assets/91530837/c31adfd6-08fb-4c78-b266-3184edca9996)<br/>


```
  security:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/checkout@master
      - name: snyk node
        uses: snyk/actions/node@master
        id: snyk
        continue-on-error: true
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --severity-threshold=critical

```
<br/><hr/><br/>
d. w trakcie budowania obrazów należy korzystać z cache w trybie inline
e. po zbudowaniu, w ramach działań w Github Actions, obraz ma być przesłany do
repozytorium na Github Packages (repo: ghcr.io).<br/>

Używanie repozytorium ghcr.io wymaga dodania tokenu github o co najmniej 3 właściwościach<br/>
- read:packages <br/>
- write:packages <br/>
- delete:packages <br/>
```
      - name: Login to GHCR
        uses: docker/login-action@v1
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.repository_owner }}
          password: ${{ secrets.GHCR_SV}}
          #password: ${{ secrets.GH_TOKEN_NEXT }}

```

```
          cache-from: type=registry,ref=${{ env.REGISTRY }}/${{ github.repository_owner }}/zadanie2:${{ steps.meta.outputs.tags }} #,${{ env.REGISTRY }}/${{ github.repository_owner }}/zadanie2:arm64
          cache-to: type=inline
```


<br/><hr/><br/>
Łańcuch Github Actions ma zostać uruchomiony tak by potwierdzić poprawność opracowanego
łańcucha działań.<br/>
łańcuch jest uruchamiany na podstawie pusha do repozytorium<br/>
![image](https://github.com/voiteckheir/zadanie2/assets/91530837/727f646f-79f8-4826-b292-125d4820a32b) <br/>
![image](https://github.com/voiteckheir/zadanie2/assets/91530837/731b69e5-0286-447c-b42a-2d6d2344ac41) <br/>
![image](https://github.com/voiteckheir/zadanie2/assets/91530837/0100db3a-26b1-41e9-8df7-dfcc5711f14a) <br/>



<br/><hr/><br/>
## CZĘŚĆ DODATKOWA

<br/><hr/><br/>
Uzupełnić workflow Github Actions o tagowanie obrazu zgodną ze schematem SemVer. 
Należy:
a. Krótko wyjaśnić wprowadzone modyfikacje,

<br/><hr/><br/>

b. Udowodnić, że kolejny proces uruchomienia łańcucha Github Action spowoduje
poprawne wygenerowanie numeru kolejnej wersji obrazu
![image](https://github.com/voiteckheir/zadanie2/assets/91530837/428d9ea0-49bc-4712-99b3-575280fcafa9)
![image](https://github.com/voiteckheir/zadanie2/assets/91530837/34e58220-df37-4735-8398-9dbf17cf58a7)

<br/><hr/><br/>
Opracowany łańcuch Github Actions z tagowaniem SemVer ma zostać uruchomiony tak by
potwierdzić poprawność opracowanego łańcucha działań.
