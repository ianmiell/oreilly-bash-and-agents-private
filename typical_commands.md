```
  1. First spec (artifacts): reading the agent and API
  cd $AGENT && grep -rn "GenerateResults" --include=*.go | head -30; git log --oneline -3   # failed: zsh glob
  grep -rn "GenerateResults\|PolicyPath\|policyPath" --include='*.go' . | grep -v _test | head -40; ls; cat go.mod | head -30
  sed -n 1,80p policy-manager/policy-manager.go; sed -n 260,420p policy-manager/policy-manager.go; grep -rn "PolicyProcessor\b\|NewPolicyProcessor" --include='*.go' .
  | grep -v _test
  cat runner/helpers.go | head -80; ls runner runner/proto; grep -n "rpc\|message" runner/proto/*.proto
  cat runner/proto/results.proto; sed -n 160,200p runner/proto/types.proto; sed -n 25,50p runner/proto/runner.proto; grep -n "ApiHelper" -A12 runner/*.go | grep -v
  _test
  grep -rn "func.*CreateEvidence\|func.*UpsertRiskTemplates" --include='*.go' . | grep -v "_test\|pb.go"; grep -rn "GenerateResults" --include='*.md' .; git log
  --oneline -1 -- policy-manager
  cat runner/result.go; sed -n 1,80p runner/util.go
  cd $API && git log --oneline -3; ls; ls sdk internal; grep -rli "artifact\|blob\|sha256" --include='*.go' . | grep -v _test | head -20
  cat sdk/evidence.go | head -60; ls internal/api/handler internal/service/relational/evidence; grep -n "evidence\|Evidence" internal/api/handler/api.go | head; grep
  -rn "RegisterHandlers" internal/api/handler/evidence*.go | head
  sed -n 95,140p internal/api/handler/api.go; sed -n 1,80p internal/service/relational/evidence/signing.go; grep -rn "type Evidence struct" -A40
  internal/service/relational/*.go | head -60
  grep -n "func \|Props\|Links\|BackMatter" internal/service/relational/evidence/signing.go | sed -n 1,60p; grep -rn "AutoMigrate" internal | grep -v _test | head -5;
  grep -n "postgres\|image:" docker-compose.yml; grep -rn "RegisterCreate\|agentIngest\|ActionIngest" internal/api/handler/api.go
  grep -rn "BodyLimit" --include='*.go' . | head -3; grep -n "Resource[A-Z][a-zA-Z]* *=" internal/authz/*.go | head -30; sed -n 150,172p internal/api/handler/api.go;
  cd ../agent && git show 753ca81 --stat | head -20

  2. Second spec (playback endpoint)
  cd $API && grep -n "open-policy-agent\|cedar" go.mod; grep -rln "opa/v1\|rego\." --include='*.go' . | grep -v _test; cd ../agent && sed -n 80,260p
  policy-manager/policy-manager.go
  grep -n "^func\|^type" policy-manager/policy-manager.go | head -20

  3. Building the playback endpoint (API)

  cd $API && git status --short | head; git branch --show-current; cat internal/api/handler/heartbeat.go | head -80; ls internal/config; sed -n 1,80p authz-roles.yaml
   # you rejected this one
  git fetch -q origin && git log --oneline -1 origin/main && git log --oneline origin/main..BCH-1366 | head && git switch -c feat/rego-playback-endpoint origin/main
  git branch --unset-upstream; cat internal/api/handler/heartbeat.go | head -90; sed -n 1,140p internal/config/config.go
  sed -n 140,260p internal/config/config.go; cat internal/config/poam.go
  sed -n 1,140p internal/authz/pdp.go; cat internal/config/authz.go | head -60; wc -l authz-roles.yaml; grep -n "evidence\|heartbeat\|anonymous\|agent"
  authz-roles.yaml
  ls internal/authz; grep -rn "heartbeat" internal/authz -l
  grep -n "heartbeat" -B3 -A12 internal/authz/manifest.yaml; grep -n "heartbeat\|anonymous\|public" internal/authz/{builtin,cedar,roles_cedar,assignments}.go
  sed -n 40,66p internal/authz/manifest.yaml; sed -n 270,360p internal/authz/manifest.yaml; grep -n "func Test" internal/authz/manifest_test.go
  internal/authz/roles_cedar_test.go
  sed -n 1,120p internal/authz/builtin.go; sed -n 78,122p internal/authz/manifest_test.go
  grep -rn "subject-template\|ResourceSubjectTemplate\|ResourceHeartbeat" --include='*.go' internal | grep -v _test; grep -rn '"heartbeat"' --include='*_test.go'
  internal/authz
  sed -n 60,130p internal/authz/cedar_test.go; grep -n "^[a-z-]*:" Makefile; grep -rn "func (g.*) Do\|func.*For(" internal/api/middleware internal/authz
  sed -n 60,110p Makefile; sed -n 1,80p sdk/client.go; grep -n "func (c \*Client) doJSONRequest" -A30 sdk/client.go
  cat api/sdk/heartbeat.go; sed -n 1,60p api/sdk/heartbeat_test.go; wc -l agent/policy-manager/*.go; grep -n "^func Test" agent/policy-manager/*_test.go
  cat policy-manager/result.go; sed -n 1,348p policy-manager/policy-manager_test.go
  sed -n 403,472p policy-manager/policy-manager_test.go; cat policy-manager/utils.go
  sed -n 1,140p internal/api/middleware/authorize.go; grep -n "func OptionalUserOrAgentJWTMiddleware" -A60 internal/api/middleware/*.go
  grep -n "func SubjectFromContext" -A30 internal/api/middleware/*.go; sed -n 1,40p internal/api/error.go; grep -rn "SetEnvPrefix" --include='*.go' .; grep -n
  "labstack/echo" go.mod
  go get github.com/open-policy-agent/opa@v1.14.1 && git diff --stat
  git diff go.mod | grep "^[-+]" | grep -v "// indirect"
  cd $(go env GOMODCACHE)/github.com/open-policy-agent/opa@v1.14.1 && grep -n "^func Time\|^func EvalTime\|^func PrintHook\|…" v1/rego/rego.go; grep -n
  "ctx.Done\|Cancel" v1/rego/rego.go; grep -n "type Hook" -A6 v1/topdown/print/print.go; grep -n "Nondeterministic *bool" v1/ast/*.go
  sed -n 1,94p internal/api/handler/api.go; grep -n "AutomaticEnv\|SetEnvKeyReplacer" cmd/root.go
  go build ./pkg/... && go vet ./pkg/...
  ls -la agent/policy-manager/testdata/001/ && cat agent/policy-manager/testdata/001/*.rego && mkdir -p api/pkg/policyeval/testdata && cp -R
  agent/policy-manager/testdata/001 api/pkg/policyeval/testdata/; grep -n testify api/go.mod
  cat > pkg/policyeval/zz_list_test.go <<EOF …   # throwaway test printing non-deterministic builtins
  go test ./pkg/policyeval -run TestZZList -v | grep ND; rm pkg/policyeval/zz_list_test.go
  go test ./pkg/policyeval/ | tail -60
  go test ./pkg/policyeval/ | grep -E "…"; sed -n 1160,1170p $OPA/v1/rego/rego.go; grep -n "r.time" $OPA/v1/rego/rego.go
  go test ./pkg/policyeval/ -run 'TestEvaluator/ignores|TestEvaluateErrors/parse' -v
  cat > pkg/policyeval/zz_test.go <<EOF …   # throwaway test printing the parse-error type; then go test -run TestZZ; rm
  go test ./pkg/policyeval/
  sed -n 45,60p cmd/root.go; sed -n 260,290p internal/config/config.go; ls internal/config/*_test.go; sed -n 1,40p internal/config/worker_test.go
  sed -n 60,110p cmd/root.go
  grep -n "viper\.\(Reset\|Set\)\|t.Parallel" internal/config/*_test.go
  grep -n "execute → import" internal/authz/pdp.go; grep -n "lineage\|control-link\|import:" internal/authz/manifest.yaml
  sed -n 225,275p internal/authz/manifest.yaml
  python3 - <<EOF …   # add the playback resource and grants to manifest.yaml; then git diff manifest.yaml
  sed -i '' 's/execute → import; export/execute → import\/playback; export/' internal/authz/pdp.go; sed -n 1,58p internal/authz/cedar_test.go; grep -n "anonymous"
  internal/authz/cedar_test.go
  sed -n 356,400p internal/authz/cedar_test.go
  python3 - <<EOF …   # add playback cases to cedar_test.go
  go test ./internal/authz/ ./internal/config/
  go mod tidy; git diff go.mod | grep "^[-+]" | grep -v indirect; go test ./internal/authz/ ./internal/config/ ./pkg/...
  grep -n "@Security\|@Router" internal/api/handler/evidence.go | head -6
  go build ./... && go vet ./internal/api/handler/ ./pkg/... ./internal/config/ ./internal/authz/
  sed -n 1,50p internal/api/handler/evidence_test.go; grep -rn "func NewServer\|func (s \*Server) API\|func (s \*Server) E()" internal/api/*.go
  go test ./internal/api/handler/ -run 'TestPlayback' -v
  sed -n 1,120p internal/api/handler/heartbeat_integration_test.go; docker info --format '{{.ServerVersion}}'
  grep -rn "func (.*IntegrationTestSuite) \|func .*AgentToken" internal/tests/*.go internal/api/handler/*_integration_test.go
  sed -n 629,680p internal/api/handler/evidence_integration_test.go
  go test ./internal/api/handler/ -tags integration -run 'TestPlaybackApi' -count=1 -v
  grep -n "json.NewDecoder\|json.Unmarshal\|func closeResponseBody\|io.ReadAll" sdk/*.go; sed -n 1,60p sdk/risk_template.go
  sed -n 330,350p sdk/client.go
  python3 - <<EOF …   # add Client.Playback to sdk/client.go; then grep roundTripFunc sdk/*_test.go
  go test ./sdk/ -run Playback -count=1
  sed -n 164,170p Makefile; ls docs; which swag; ls $(go env GOPATH)/bin | grep -i swag
  make swag; git status --short; git diff --stat docs/
  git diff docs/swagger.yaml | grep …
  grep -n "CCF_STRICT_DISABLE\|CCF_AUTHZ_DRIVER\|^#" README.md; sed -n 58,78p README.md; tail -25 .env.example
  cat >> .env.example <<EOF …   # playback settings
  cat > pkg/policyeval/zz_doc_test.go <<EOF …   # throwaway test checking the doc's example response; then go test; rm
  go test ./... | grep -v "^ok\|no test files"; go test -race -count=1 ./pkg/policyeval/ ./sdk/; go test -race -count=1 -run Playback ./internal/api/handler/
  sed -n 135,143p Makefile; make lint
  go test -tags integration -count=1 ./internal/authz/; go test -tags integration -count=1 -run 'Permission|Authz|Cedar|Evidence' ./internal/api/handler/
  go test -tags integration -count=1 ./... > $S/integration.log 2>&1; echo "exit=$?" >> $S/integration.log   # in background
  git diff -- cmd internal sdk/client.go
  tail -5 $S/integration.log

  4. Committing and pushing the API change
  tail -3 $S/integration.log; grep -E "^(FAIL|--- FAIL|ok)" $S/integration.log
  git status --short && git branch --show-current && git log --oneline -3
  git add -A && git commit -q -F - <<EOF … && git log --oneline -1 && git push -u origin feat/rego-playback-endpoint

  5. The "still to do" parts (agent PR, OPA check)
  tail -2 $S/integration.log; grep -E "^(FAIL|--- FAIL)" $S/integration.log | head; grep -c "^ok" $S/integration.log
  cd $AGENT && git status --short && git branch --show-current && git fetch -q origin && git log --oneline -1 origin/main && ls .github/workflows && grep -n
  "^[a-z-]*:" Makefile
  tail -1 $S/integration.log; grep -E "^(FAIL|--- FAIL)" $S/integration.log | wc -l; cat .github/workflows/main.yml; sed -n 35,50p Makefile
  sed -n 440,586p policy-manager/policy-manager.go; grep -rn "prepareForEval\|loaderOptions\|pm.policyData\|\.logger" policy-manager/*.go
  git switch -q -c feat/use-shared-policyeval origin/main && git branch --unset-upstream; GOFLAGS=-mod=mod GOPRIVATE=github.com/compliance-framework go get
  github.com/compliance-framework/api@12a10c1; grep -n "compliance-framework/api\|open-policy-agent/opa \|mapstructure" go.mod
  python3 - <<EOF …   # replace the evaluation code in policy-manager.go and result.go with aliases; then goimports -w; go build ./...
  sed -i '' 's#…go-hclog"#…policyeval"\n…go-hclog"#' policy-manager/policy-manager_test.go && gofmt -l .; go vet ./...; go test ./...
  git diff --stat && sed -n 1,50p policy-manager/policy-manager.go
  git diff go.mod | grep "^[-+]" | grep -v "// indirect"
  sed -n 472,520p policy-manager/policy-manager_test.go
  go test ./policy-manager/ -run TestGenerateResultsMatchesPlayback -v
  go mod graph | awk '…opa…'; sed -n 1,28p Makefile
  make check-opa-version; cp go.mod $S/go.mod.bak && sed -i '' 's#opa v1.14.1#opa v1.15.0#' go.mod && make check-opa-version; cp $S/go.mod.bak go.mod; git diff --stat
  go.mod
  go build ./... && go vet ./... && gofmt -l . && go test ./...; git status --short
  git add -A && git commit -q -F - <<EOF … && git push -u origin feat/use-shared-policyeval
  6. Opening the PRs
  gh pr create --base main --head feat/rego-playback-endpoint --title "feat: add Rego playback endpoint" --body-file - <<EOF …   # failed: 401
  gh auth status
  gh pr create … (same command)   # failed: #463 already existed
  gh pr view 463 --json number,title,author,state,createdAt,body,headRefOid,url
  cd $AGENT && gh pr list --head feat/use-shared-policyeval --state all --json number,url; cd ../api && gh pr view 463 --json reviews,comments --jq …; gh api
  repos/compliance-framework/api/pulls/463/comments --jq 'length'
  cd $AGENT && gh pr create --base main --head feat/use-shared-policyeval --title "feat: evaluate policies with the API's shared policyeval package" --body-file -
  <<EOF …   # created #92
  gh api repos/compliance-framework/api/pulls/463/comments --jq '…'   # three variants, reading CodeRabbit's comments

  7. Planning the hashing work, and saving a memory
  grep -n "^func [A-Z].*Middleware" api/internal/api/middleware/*.go | grep -v _test; grep -rn "MaxRecvMsgSize\|MaxCallRecvMsgSize\|GRPCServer:" agent --include='*.go'
  | grep -v _test
  M=…/memory/MEMORY.md; test -f $M && cat $M; echo '- [Artifact digests use Go canonical JSON](…)' >> $M

  8. Building artifact hashing (API and agent)
  which buf; grep -n "func (c \*Client) executeRequest" -A30 api/sdk/client.go; grep -n "NewApiHelper\|AcceptAndServe\|broker.Dial" agent/cmd/*.go agent/runner/*.go |
  grep -v _test
  cat buf.gen.yaml buf.yaml; head -8 runner/proto/results.pb.go runner/proto/results_grpc.pb.go; ls ~/go/bin
  GOBIN=$S/bin go install github.com/bufbuild/buf/cmd/buf@v1.57.2; GOBIN=$S/bin go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.36.11; GOBIN=$S/bin go
  install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.3.0; ls $S/bin
  cd $API && git status --short | head -3; git switch -q -c feat/policy-artifacts feat/rego-playback-endpoint && git branch --unset-upstream; git log --oneline -1;
  grep -n "type DirectoryLoader interface" -A6 $OPA/v1/bundle/*.go; grep -n "^func NewDescriptor\|^func NewCustomReader\|…" $OPA/v1/bundle/*.go
  grep -n "^func New.*Loader\|gzip.NewReader\|…NextFile" $OPA/v1/bundle/file.go; sed -n '/func (t \*tarballLoader) NextFile/,/^}/p' $OPA/v1/bundle/file.go | head -60
  cd pkg/artifact && mkdir -p testdata/bundle/lib testdata/bundle/config && cat > testdata/bundle/policy.rego <<EOF …; cat > …/helpers.rego <<EOF …; cat > …/data.json
  <<EOF …; find testdata -type f | sort
  go test ./pkg/artifact/
  cat > pkg/artifact/zz_test.go <<EOF …; OUT=$S/b.tar go test ./pkg/artifact -run TestZZ -v; rm pkg/artifact/zz_test.go; tar -tvf $S/b.tar; grep -c
  "PaxHeader\|mtime=\|atime=" $S/b.tar; wc -c $S/b.tar
  python3 - <<EOF …   # pin the golden digests in artifact_test.go; then go test ./pkg/artifact/ -count=1
  sed -n 30,60p internal/service/migrator.go; sed -n 40,60p internal/tests/migrate.go; grep -n "type Heartbeat struct" -A6 internal/service/*.go; ls
  internal/service/relational
  grep -n "Heartbeat{}\|relational.Evidence{}," internal/service/migrator.go internal/tests/migrate.go; grep -n "func (t \*TestMigrator) Down\|DropTable" -A3
  internal/tests/migrate.go; head -40 internal/service/relational/control_link.go
  python3 - <<EOF …   # register Artifact in both migrators; then grep; sed -n 1128,1134p internal/service/migrator.go
  python3 - <<EOF …   # artifact config in config.go and root.go; cat >> internal/config/playback_test.go <<EOF …; go test ./internal/config/
  grep -rn "type AgentAuthContext" -A6 internal/api/middleware/; grep -n "type Agent struct" -A5 internal/service/relational/agents.go
  grep -n "type UUIDModel" -A3 internal/service/relational/*.go; sed -n 21,60p internal/api/middleware/agent_ingest.go
  cat >> pkg/artifact/artifact.go <<EOF …   # DigestHeader and Info; then gofmt -l pkg/
  sed -i '' 's#execute → import/playback; export#…ingest → artifact; export#' internal/authz/pdp.go && python3 - <<EOF …   # pdp.go, manifest.yaml, api.go; then go
  build ./... && go vet …
  python3 - <<EOF …   # artifact cases in cedar_test.go; then go test ./internal/authz/
  python3 - <<EOF …   # ssp-subscriber cases; then go test ./internal/authz/
  sed -n 72,160p sdk/client.go; grep -n "executeStreamingRequest\|executeRequest(" sdk/*.go | grep -v _test
  python3 - <<EOF …   # add a headers parameter to sdk/client.go; then go build ./sdk/; grep … sdk/*_test.go
  python3 - <<EOF …   # add Client.Artifact; then go build ./... && go test ./sdk/
  go test ./sdk/ -count=1 -run Artifact -v
  gofmt -w internal/api/handler/artifacts_integration_test.go && go test ./internal/api/handler/ -tags integration -run 'TestArtifactApi' -count=1 -v
  cat >> .env.example <<EOF …; make swag | grep -i "error\|warn"; git diff --stat docs/; git diff docs/swagger.yaml | grep "^-"
  make lint; go test ./... | grep -v "^ok\|no test files"
  cd $AGENT && git status --short; git switch -q -c feat/policy-artifacts feat/use-shared-policyeval && git branch --unset-upstream; cat > $S/buf.gen.local.yaml <<EOF
  …; PATH=$S/bin:$PATH buf generate --template $S/buf.gen.local.yaml; git status --short
  cat > $S/go.work <<EOF …; GOWORK=$S/go.work go build ./...; python3 - <<EOF …   # new RPC in results.proto; then PATH=$S/bin:$PATH buf generate --template
  $S/buf.gen.local.yaml && git status --short
  sed -n 1,20p runner/grpc.go; sed -n 44,190p runner/grpc.go
  grep -rn "UpsertSubjectTemplates(ctx\|) UpsertSubjectTemplates(" --include='*.go' . | grep -v pb.go
  grep -rn "CreateEvidence(" --include='*_test.go' .; grep -n "func (b \*GRPCBroker) Dial" $GOMODCACHE/github.com/hashicorp/go-plugin@v1.7.0/grpc_broker.go
  python3 - <<EOF …   # new RPC, client and server in runner/grpc.go
  sed -n 1404,1432p cmd/agent.go; sed -n 1496,1560p cmd/agent.go
  cd $API && python3 - <<EOF …   # ArtifactStatusError in sdk/artifact.go and its test; then go test ./sdk/
  cd $AGENT && python3 - <<EOF …   # StorePolicyArtifacts in runner/result.go, WithPolicyPaths in cmd/agent.go; then GOWORK=$S/go.work go build ./...
  sed -n 44,175p policy-manager/policy-manager.go; sed -n 175,220p policy-manager/policy-manager.go | grep -n Props
  python3 - <<EOF …   # store option, digest props and the Props fix in policy-manager.go; then GOWORK=… go build && go vet
  python3 - <<EOF …   # fix json import in artifacts_test.go; then GOWORK=… go test ./policy-manager/
  sed -n 1,60p runner/result_test.go; grep -n "^func Test" runner/grpc_test.go
  python3 - <<EOF …   # extract newApiHelperGRPCServer; then grep bufconn / grpc version
  GOWORK=… go test ./runner/ -count=1 -run 'StorePolicyArtifacts|LargeInput' -v
  sed -i '' 's/… = 64 << 20/… = 4 << 20/' runner/grpc.go && go test ./runner/ -run LargeInput; sed -i '' 's/… = 4 << 20/… = 64 << 20/' runner/grpc.go && grep -n
  "MaxApiHelperMessageBytes =" runner/grpc.go
  cat >> runner/artifacts_test.go <<EOF …; python3 - <<EOF …   # imports; then gofmt -l .; go vet ./...; go test ./...
  ls docs; grep -n "^#" docs/*.md; grep -rn "GenerateResults\|NewPolicyProcessor" docs README.md
  GOWORK=… go test -race -count=1 ./runner/ ./policy-manager/; cd ../api && go test -race -count=1 ./pkg/... ./sdk/; git status --short; cd ../agent && git status
  --short
  cd $API && go test -tags integration -count=1 ./... > $S/integration-artifacts.log 2>&1; echo "exit=$?" >> …   # in background

  9. Investigating the integration failures
  tail -1 $L; grep -cE "^(FAIL|--- FAIL)" $L; grep -c "^ok" $L
  grep -nE "^(FAIL|--- FAIL)|^\s+--- FAIL" $L | head -20
  sed -n 630,660p $L; sed -n 835,850p $L; sed -n 1985,2003p $L; grep -n "panic: test timed out" $L
  cd $API && go test -tags integration -count=1 -p 1 -timeout 40m ./internal/api/handler/ ./internal/api/handler/oscal/ > $S/integration-rerun.log 2>&1; echo "exit=$?"
  >> …   # in background
  tail -1 $S/integration-rerun.log; grep -E "^(ok|FAIL|--- FAIL)" $S/integration-rerun.log

  10. The skill demo
  cd ~/.claude/skills/synced/…/iantestskill && python3 scripts/greet.py "Ian"; echo "---"; cat references/skill-anatomy.md; echo "---"; cat assets/greeting-card.txt
```


SP=/private/tmp/claude-501/-Users-imiell-git-gtd/3b49ff9f-a88d-4d5a-83a4-5ca0d1973950/scratchpad; time bash $SP/harness.sh base 2>&1 | tail -3

SP=/private/tmp/claude-501/-Users-imiell-git-gtd/3b49ff9f-a88d-4d5a-83a4-5ca0d1973950/scratchpad; cat /private/tmp/claude-501/-Users-imiell-git-gtd/3b49ff9f-a88d-4d5a-83a4-5ca0d1973950/tasks/bgamzhjnu.output; pgrep -fl harness.sh | head -2; wc -l $SP/base.out; grep -n '^=== ' $SP/base.out | tail -3; grep -n -A3 '=== \[web\] gtd info\|=== \[web\] gtd 4640\|=== \[web\] gtd go' $SP/base.out | head -20

```
python3 - <<'EOF'
      def edit(p, pairs):
          s=open(p).read()
          for old,new in pairs:
              assert s.count(old)==1, (p,old)
              s=s.replace(old,new)
          open(p,'w').write(s)
      dead='''set +u
      if [[ "" == "" ]]
      then
        echo "Argument required to gtd-newtask - subject"
        echo ""
        sleep 2 && ./gtd-help
      fi

      '''
      for p in ['gtd-commit','gtd-push','gtd-pull']:
          edit(p,[(dead,'')])
      edit('gtd-info',[('''if [[ "" == "" ]]''','''if [[ "" == "" ]]'''),('''readonly TASK_NO=""''','''readonly
      TASK_NO=""''')])
      for p,name in [('gtd-edit','gtd-edit'),('gtd-vi','gtd-vi')]:
          edit(p,[('echo "Argument required to gtd-go - task number"',f'echo "Argument required to {name} - task number"')])
      edit('gtd-todos',[('''FILES=""''','''FILES="" || true'''),
       ('done | sort -n)"','done | sort -nu)"')])
      edit('gtd',[('''  source gtd-go ""''','''       source gtd-go go ""''')])
      EOF
      for f in gtd gtd-commit gtd-push gtd-pull gtd-info gtd-edit gtd-vi gtd-todos; do bash -n  || echo SYNTAX ; done; cat gtd-commit; cd
      /Users/imiell/git/gtd && GTD_DISPLAY_CONTEXT=web bin/gtd todos </dev/null 2>&1 | head -8; GTD_DISPLAY_CONTEXT=web bin/gtd info 4640
      </dev/null 2>&1 | head -6; GTD_DISPLAY_CONTEXT=web bin/gtd 4640 </dev/null 2>&1 | head -3)
```

cd /Users/imiell/git/gtd/bin && sed -i '' 's/FILES="\$(grep -nwIEl TODO/FILES="$(grep -snwIEl TODO/' gtd-todos && grep -n 'grep -snwIEl'
gtd-todos && bash -n gtd-todos && ls calendar; ls -la mail.py wipe-content.sh | awk '{print $NF}'; head -5 wipe-content.sh
