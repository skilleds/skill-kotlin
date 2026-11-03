# Piso de Kotlin (schematize-kotlin) — sempre-on

> No que é de **app** (offline, IAM, push, loja, teste no device), quem manda é a
> **`schematize-mobile`**. Aqui é o que é de **linguagem**.

1. **`!!` é VETADO** — é um NPE que você escolheu. `?.`, `?:`, `requireNotNull(x) { "por quê" }`.
2. **Fronteira com Java mente:** platform type não é checado — trate como anulável na borda.
3. **`GlobalScope` é VETADO**; escopo estruturado sempre (`viewModelScope`, `coroutineScope`).
4. **`CancellationException` não é erro** — relance-o; `runCatching` engole por default.
5. **`Dispatchers` no repositório**, não no chamador; **`runBlocking` VETADO** em app.
6. **`sealed` + `when` sem `else`** — com `else` o compilador cala quando um caso novo aparece.
7. **`catch (e: Exception) { }` VETADO**; `catch (Throwable)` engole `Error` da JVM e o cancelamento.
8. **Android:** nada de `Context`/`Activity` estático; nada de I/O na main thread; `collect` com
   `repeatOnLifecycle`.
9. **Segurança:** segredo nunca no APK; `SharedPreferences` é **XML em claro** (use
   `EncryptedSharedPreferences`/Keystore); `addJavascriptInterface` com remoto é VETADO;
   `android:exported` **explícito**.
10. **Build:** version catalog + lock, **wrapper com checksum**, `allWarningsAsErrors`, toolchain
    declarada, R8 com `keep` **testado**.
11. <!-- herdado:engineering/orquestracao:curto -->**Orquestrador não desenvolve; subagent barato executa.** O agent principal só planeja, despacha e revisa; ação onerosa vira micro-tasks para o executor mais barato que resolve: `haiku` (mecânico) / `sonnet` (código; falhou → mesmo subagent corrige, até 2 rodadas) → Codex `gpt-5.6-terra` → Cursor → subagent `opus` (motivo) → `park`; **visual vai direto ao Codex**; **imagem nunca pelo Claude: detectar Codex/Gemini e perguntar ao usuário qual usar (Codex é a sugestão), nunca API de imagem paga**; papéis em `agentes.toml`. No overdev, cada item do checklist vai a um subagent e o principal revisa antes do `- [x]`. **Sem frota ociosa:** idle com pendência volta ao trabalho; dependente de outro agent → mata e enfileira com gatilho; terminou → mata (§9.6). Detalhe: `schematize-engineering` → `references/orquestracao.md` §9.<!-- /herdado -->

<!-- herdado:engineering/pisos-22-29:bloco -->**Herda os pisos 22–29 da `schematize-engineering`** (texto completo em `assets/CLAUDE.md` dela): 22 tudo no git — sem remote, push falho ou HEAD≠origin a demanda NÃO se conclui; 23 deploy só do git (VETADO scp/rsync/tar|ssh/docker save|load/git bundle); 24 o ops é o gate da IA — a IA nunca muda a VPS direto nem escreve SQL, só operação nomeada (`ssh vps "ops <op>"`); 25 sem agents inúteis (entregou, morreu); 26 relatório a cada 30 min e handoff a cada 6 h; 27 CI remoto só antes do hml, a pedido ou em tag (`workflow_dispatch` + `hml-*`/`v*`; VETADO `pull_request`, `push` em branch, `workflow_run`, cron sem justificativa); 28 microserviço autônomo (fila durável + 7 testes de resiliência); 29 build/teste na máquina dev/hml quando existe (ponte só no workspace de build, nunca prd) + higiene (`scripts/higiene.sh`).<!-- /herdado -->

Gate: `bash .claude/skills/schematize-kotlin/scripts/check-kotlin.sh .`
