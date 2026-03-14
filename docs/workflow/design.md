# 開発ワークフロー設計書 (Development Workflow Design)

本リポジトリにおける開発のライフサイクル、AI Skill の役割、およびコンテキスト継続性の設計思想を定義します。

## 1. 全体状態遷移 (Overall State Transitions)

プロジェクトのライフサイクルにおける各フェーズの状態変化と、中断・再開 (`/save`, `/resume`) を含むフローです。

```mermaid
stateDiagram-v2
    [*] --> Idle: AI 起動 (コンテキストなし)
    
    state "中断・待機状態 (Idle)" as Idle
    note right of Idle: 前回の進捗 (Issue) が保存されている状態
    
    state "実作業中 (Active)" as Working {
        state "Phase 0: 計画" as Phase0

        state "Phase 1: 準備・着手" as Phase1
        state "Phase 2: 開発" as Phase2
        state "Phase 3: レビュー・マージ" as Phase3

        Phase0 --> Phase1: Issue 起票
        Phase1 --> Phase2: ブランチ切替
        Phase2 --> Phase3: PR 発行
        Phase3 --> Phase2: 修正依頼
        Phase3 --> Phase0: マージ完了 (次タスクへ)
    }

    
    Idle --> Working: 依頼受信 / /resume (作業開始)
    
    Working --> Idle: /save (中断・進捗保存)
```




## 2. 全体ライフサイクル (Lifecycle Overview)

登場人物間のやり取りを中心とした、時間軸での概要シーケンスです。

```mermaid
sequenceDiagram
    participant U as ユーザー (User)
    participant A as Antigravity (AI)
    participant G as GitHub (Cloud)

    rect rgb(255, 245, 235)
        Note over U, G: 0. 計画 (Planning)
        A->>G: 既存計画 (Roadmap) の読込
        A->>U: 追加・修正案の提示
        U->>A: 承認・修正依頼
        A->>G: Issue 起票 (Source of Truth)
    end

    rect rgb(235, 245, 255)
        Note over U, G: 1. 準備・着手 (Prep & Kickoff)
        A->>U: 次にやる事の提案 (Issue提示)
        U->>A: 着手対象を **選択**
        A->>G: ブランチ作成 & 切替
        Note right of G: ブランチ切替をもって着手完了
    end

    rect rgb(240, 240, 240)
        Note over U, G: 2. 開発 (Development)
        A->>G: 実装 ・ テスト ・ Push
        A->>G: PR 発行 (Ready for review)
        Note right of G: PR 発行をもって開発完了
    end

    rect rgb(235, 255, 235)
        Note over U, G: 3. レビュー・マージ (Review & Merge)
        U->>G: PR レビュー
        G-->>A: 修正依頼 (必要に応じて 2 へ)
        U->>G: マージ (Merge)
        U->>A: /cleanup 依頼
        A->>G: ブランチ削除
        Note right of G: クリーンアップをもって完了
    end
```

## 3. 着手フロー (Kickoff Flow)

`kickoff-task` Skill が担う、作業開始時の詳細な論理フローです。

```mermaid
flowchart TD
    Start([開発依頼・セッション開始]) --> ReviewPlan[0. 計画: 既存計画のレビュー & Issue起票]
    ReviewPlan --> ReadSkill[1. 準備・着手: kickoff-task 読込]
    ReadSkill --> ProposeIssue[次の一手の提案]
    ProposeIssue --> SelectIssue{ユーザーによる選択}
    SelectIssue --> CreateBranch[ブランチ作成 & チェックアウト]
    CreateBranch --> UpdateRoadmap[Roadmapを着手中へ]
    UpdateRoadmap --> InitTaskMD[task.md 初期化]
    InitTaskMD --> DraftPR[Draft PR作成]
    DraftPR --> End([着手完了])
```

## 4. 完了フロー (Wrapup Flow)

`wrapup-task` Skill が担う、品質確保と最終化の詳細なフローです。

```mermaid
flowchart TD
    Complete([実装完了の判断]) --> ReadSkill[wrapup-task 読込]
    ReadSkill --> Validation[最終動作検証]
    Validation --> UpdateRoadmap[Roadmapを完了へ]
    UpdateRoadmap --> CommitPush[Commit & Push]
    CommitPush --> ReadyPR[gh pr ready]
    ReadyPR --> Report[完了報告]
    Report --> End([レビュー待ち])
```

## 5. 設計上の重要原則

1. **GitHub Issue as the Source of Truth**: 設計判断やタスクリストの詳細は常に Issue に集約され、AI の `task.md` はその一時的な写しに過ぎません。
2. **Early Draft PR**: コードの透明性を保つため、最初の Commit 直後に Draft PR を作成します。
3. **Skill-Driven Execution**: 複雑な手順は Skill (`SKILL.md`) にカプセル化し、実行時の安定性を担保します。
