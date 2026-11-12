# macos-sign-probe

Prova de que **este harness consegue macOS de verdade na nuvem** — sem Mac local e sem máquina virtual — para
compilar e **assinar** app iOS.

## O que foi medido (run real, 2026-11-12)

| Item | Valor observado |
|---|---|
| Runner | `macos-latest` do GitHub Actions |
| macOS | **26.7.2** |
| Xcode | **26.6** (build 17F113) |
| SDK do iOS | **26.5** |
| `codesign`, `security`, `xcrun` | presentes |
| Identidades de assinatura na máquina | **0** — runner vem limpo; os certificados entram por secret |
| Simuladores prontos | iPhone 17 Pro / Pro Max / 17 / 17e / 16e |

Run: <https://github.com/LMPrado-DZ23/macos-sign-probe/actions/runs/43248400331>

## Por que isto e não uma VM de macOS no PC

| Caminho | Situação |
|---|---|
| **Runner macOS na nuvem** (este repo) | ✅ funciona, é o que este repositório prova |
| VM de macOS no Windows (VMware/VirtualBox + patches) | ❌ viola a licença da Apple (macOS virtualizado só em hardware Apple); quebra em updates do Xcode; e nesta máquina o hipervisor já está ocupado pelo WSL2/Docker — desligá-lo derrubaria o resto do ambiente |
| Mac alugado (MacStadium, AWS EC2 mac, MacinCloud) | 💰 funciona, custo por hora/dia |

## Custo

| Repositório | Minutos macOS |
|---|---|
| **público** | ilimitado (grátis) |
| privado | 2.000 min/mês no plano free; macOS consome 10x (200 min efetivos) |

## Como assinar de verdade

1. Ter **Apple Developer Program** (US$ 99/ano) — sem isso não existe certificado de distribuição.
2. Exportar o certificado `.p12` + o provisioning profile `.mobileprovision`.
3. Converter para base64 e gravar como secrets do repositório:
   - `BUILD_CERTIFICATE_BASE64`
   - `P12_PASSWORD`
   - `KEYCHAIN_PASSWORD` (qualquer valor)
   - `PROVISIONING_PROFILE_BASE64`
   - `APPLE_TEAM_ID`
4. Rodar o workflow [`sign-template.yml`](.github/workflows/sign-template.yml) informando o scheme e o método
   (`app-store`, `ad-hoc`, `development`, `enterprise`).
5. O `.ipa` assinado sai como artefato do run.

## Alternativas ao Actions

| Serviço | macOS grátis | Assina? |
|---|---|---|
| **EAS Build** (Expo) | fila grátis; gerencia certificado via App Store Connect API | sim |
| **Codemagic** | ~500 min/mês | sim |
| **Bitrise** | plano free limitado | sim |
| **Xcode Cloud** (Apple) | 25 h/mês com Developer Program | sim |

> Assinar é necessário apenas para **instalar em iPhone físico** e publicar. Build de **simulador** (`.app` para
> o Appetize) não exige certificado nenhum.
