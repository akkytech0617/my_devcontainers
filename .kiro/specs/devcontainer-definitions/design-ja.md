# 設計ドキュメント

## 概要

この設計は、個人開発プロジェクト用のdevcontainer定義ファイルを作成するためのアーキテクチャを概説します。システムは、最初にJavaScript/TypeScript Web開発をサポートし、将来的にPython、Tauri、Flutter、Java環境への拡張基盤を持つ、標準化されたモジュラーdevcontainer設定を生成します。

## アーキテクチャ

### コアコンポーネント

1. **テンプレートジェネレーター**: 事前定義されたテンプレートに基づいてdevcontainer.jsonファイルを作成
2. **設定マネージャー**: 共通ツールと環境セットアップを処理
3. **拡張機能マネージャー**: VS Code拡張機能とカスタマイゼーションを管理
4. **ドキュメントジェネレーター**: セットアップとカスタマイゼーションのドキュメントを作成

### 設計原則

- **モジュラー性**: 各環境タイプは自己完結型だが、共通コンポーネントを共有
- **拡張性**: 新しい開発環境の追加が容易
- **一貫性**: すべてのdevcontainerタイプで標準化された構造
- **カスタマイズ性**: ユーザーのカスタマイゼーションと拡張のための明確なパス

## コンポーネントとインターフェース

### 1. テンプレート構造

```
.devcontainer/
├── devcontainer.json          # メイン設定
├── Dockerfile                 # カスタムFedoraベースイメージ
└── docker-compose.yml         # マルチサービスセットアップ用オプション
```

### 2. JavaScript/TypeScript Web開発テンプレート

#### ベース設定
- **ベースイメージ**: Fedoraベース（`fedora:latest`）でのカスタムDockerfile
- **パッケージマネージャー**: dnf（Fedoraのパッケージマネージャー）
- **Node.js**: NVM（Node Version Manager）で管理、バージョン切り替えが容易
- **Python**: UV（Python Version Manager）で管理、バージョン切り替えが容易
- **パッケージマネージャー**: npm（デフォルト）、npm install経由でyarnとpnpmも利用可能

#### 開発ツール
- **テスト**: Jest、Vitest
- **コード品質**: ESLint、Prettier
- **VS Code拡張機能**:
  - `dbaeumer.vscode-eslint`
  - `esbenp.prettier-vscode`
  - `ms-vscode.vscode-typescript-next`
  - `bradlc.vscode-tailwindcss` (オプション)

#### Dockerfile設定
```dockerfile
FROM fedora:latest

# 基本開発ツールのインストール
RUN dnf update -y && \
    dnf install -y git curl wget unzip sudo python3 python3-pip && \
    dnf clean all

# 非rootユーザーの作成
RUN useradd -m -s /bin/bash vscode && \
    echo 'vscode ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER vscode
WORKDIR /home/vscode

# NVM（Node Version Manager）のインストール
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash && \
    echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.bashrc && \
    echo '[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"' >> ~/.bashrc

# UV（Python Version Manager）のインストール
RUN curl -LsSf https://astral.sh/uv/install.sh | sh && \
    echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc

# NVM経由でデフォルトNode.jsバージョンを設定
RUN bash -c 'source ~/.nvm/nvm.sh && nvm install 20 && nvm use 20 && nvm alias default 20'
```

### 3. 共通開発ツールモジュール

#### Git設定
- 基本設定済みのGit
- 追加拡張機能なしの基本Git機能

#### シェル環境
- デフォルトbashシェル（軽量）
- 基本エイリアスと環境変数
- シンプルなプロンプト設定

#### ユーティリティ
- curl、wget、unzip
- Docker CLI（Docker-outside-of-Dockerシナリオ用）
- 基本開発ユーティリティ

#### VS Code必須拡張機能
- `ms-azuretools.vscode-docker`
- `ms-vscode-remote.remote-containers`

### 4. 拡張機能とカスタマイゼーションシステム

#### テンプレートカスタマイゼーションポイント
1. **環境変数**: devcontainer.jsonを通じて設定可能
2. **ポートフォワーディング**: 異なるサービス用の設定可能ポート
3. **作成後コマンド**: カスタマイズ可能なセットアップスクリプト
4. **追加機能**: モジュラー機能追加

#### サービス統合サポート
- データベースサービス（PostgreSQL、MySQL、MongoDB）
- キャッシュ用Redis
- マルチサービスセットアップ用Docker Compose統合

## データモデル

### DevContainer設定スキーマ

```typescript
interface DevContainerConfig {
  name: string;
  image?: string;
  dockerfile?: string;
  features: Record<string, FeatureConfig>;
  customizations: {
    vscode: {
      extensions: string[];
      settings?: Record<string, any>;
    };
  };
  forwardPorts?: number[];
  postCreateCommand?: string;
  remoteUser?: string;
  remoteEnv?: Record<string, string>;
}

interface FeatureConfig {
  version?: string;
  [key: string]: any;
}
```

### テンプレートメタデータ

```typescript
interface TemplateMetadata {
  id: string;
  name: string;
  description: string;
  category: 'web' | 'mobile' | 'desktop' | 'backend';
  language: string[];
  frameworks?: string[];
  services?: string[];
  complexity: 'basic' | 'intermediate' | 'advanced';
}
```

## エラーハンドリング

### 設定検証
- devcontainer.jsonのJSONスキーマ検証
- 機能互換性チェック
- イメージ可用性確認

### ランタイムエラーハンドリング
- 不足機能の優雅なフォールバック
- 設定問題の明確なエラーメッセージ
- ネットワーク依存操作の自動リトライメカニズム

### ユーザーフィードバック
- コンテナ作成中の詳細ログ
- 長時間実行操作の進行インジケーター
- 一般的な問題のトラブルシューティング用明確なドキュメント

## テスト戦略

### ユニットテスト
- テンプレート生成ロジック
- 設定検証
- 機能統合テスト

### 統合テスト
- 完全なdevcontainer作成と起動
- VS Code拡張機能読み込み
- サービス接続性（データベースなど）

### エンドツーエンドテスト
- 完全な開発ワークフローテスト
- マルチサービス環境テスト
- クロスプラットフォーム互換性（Windows、macOS、Linux）

### テスト環境
- ローカルDockerテスト
- GitHub Codespaces互換性
- 各種VS Codeバージョン

## 実装フェーズ

### フェーズ1: JavaScript/TypeScript基盤
1. 基本JavaScript/TypeScriptテンプレート
2. 共通ツール統合
3. 必須VS Code拡張機能
4. ドキュメントとカスタマイゼーションガイド

### フェーズ2: サービス統合
1. データベースサービステンプレート（PostgreSQL、MySQL）
2. Docker Compose統合
3. ポートフォワーディング設定
4. 環境変数管理

### フェーズ3: 将来の環境サポート
1. Python開発環境
2. Tauri（Rustベース）デスクトップ開発
3. Flutterモバイル開発
4. Java開発環境

### フェーズ4: 高度な機能
1. 多言語プロジェクトサポート
2. マイクロサービス開発テンプレート
3. CI/CD統合テンプレート
4. パフォーマンス最適化ツール

## セキュリティ考慮事項

### コンテナセキュリティ
- 公式Microsoftdevcontainerイメージの使用
- 定期的なベースイメージ更新
- 最小権限原則

### 開発セキュリティ
- セキュアなデフォルト設定
- 環境変数のベストプラクティス
- シークレット管理ガイドライン

### ネットワークセキュリティ
- 制御されたポート公開
- セキュアなサービス間通信
- VPNとプロキシサポートドキュメント

## パフォーマンス最適化

### コンテナ起動
- 最適化されたベースイメージ
- 効率的なレイヤーキャッシュ
- 並列機能インストール

### 開発体験
- 高速ファイル同期
- 最適化された拡張機能読み込み
- 効率的なリソース利用

### スケーラビリティ
- モジュラーテンプレートシステム
- 効率的なテンプレート生成
- 繰り返し操作のキャッシュメカニズム