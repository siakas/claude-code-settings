# AI Agent 実行ガイドライン

**最重要**：自律的に判断・実行。確認は最小限に。React/Next.js/TypeScript 開発に特化。

## コア原則

- **即座実行** — 既存コンポーネント・ページの編集は迷わず着手
- **型安全性優先** — TypeScript の型定義を徹底、any 使用禁止
- **React ベストプラクティス** — Hooks、コンポーネント設計の原則遵守
- **Next.js 規約準拠** — App Router、Server Components の活用
- **品質と一貫性** — ESLint、Prettier による自動チェック

## 基本設定

- 言語：日本語（技術用語は英語）
- スペース：日本語と半角英数字間に半角スペース
- 文体：ですます調、句読点は「。」「、」
- 絵文字：過度な使用は避ける
- Cursor では `.windsurf/` を除外
- Windsurf では `.cursor/` を除外

### 略語解釈

- `y` = はい（Yes）
- `n` = いいえ（No）
- `c` = 続ける（Continue）
- `r` = 確認（Review）
- `u` = 元に戻す（Undo）

## Git とコミット設定

### コミットメッセージ

- **必ず日本語で記述**
- **形式**: 「動詞: 変更内容の説明」
 - 例: `追加: ユーザー認証機能`
 - 例: `修正: ページネーションのバグ`
 - 例: `更新: APIエンドポイントの最適化`
 - 例: `削除: 不要なコンポーネント`
 - 例: `リファクタリング: カスタムフックの整理`

### コミット規律

コミットは以下の条件をすべて満たした時のみ実行：

- ✅ すべてのテストがパス
- ✅ TypeScript エラーゼロ
- ✅ ESLint/Prettier の警告ゼロ
- ✅ 単一の論理的作業単位を表現
- ✅ コミットメッセージが変更内容を明確に説明

## 実行ルール

### 即座実行（確認不要）

- **コンポーネント操作**：バグ修正、リファクタリング、パフォーマンス改善
- **スタイリング**：CSS Modules、Tailwind CSS の調整
- **型定義**：interface、type の追加・修正
- **カスタムフック**：ロジックの抽出と Hook 化
- **状態管理**：Context、Reducer の実装
- **依存関係**：npm/yarn/pnpm パッケージの追加・更新
- **テスト**：Jest、React Testing Library でのテスト実装
- **ドキュメント**：README、コメントの更新（日本語優先）

### 確認必須

- **新規ページ作成**：ルーティング構造への影響を説明
- **API Routes**：新規エンドポイントの追加
- **認証実装**：NextAuth.js などの認証機能
- **SSR/SSG 切替**：レンダリング方式の変更
- **ビルド設定**：next.config.js の大幅な変更
- **環境変数**：.env ファイルの追加・変更
- **外部 API 連携**：新規サービスとの接続
- **データベース**：スキーマ変更、マイグレーション

## コード品質

### 命名規則とコメント

```typescript
// ✅ 推奨：日本語コメントで意図を明確に
interface ButtonProps {
 /** ボタンのスタイルバリエーション */
 variant?: 'primary' | 'secondary';
 /** クリック時のハンドラー */
 onClick?: () => void;
 /** ボタン内に表示する内容 */
 children: React.ReactNode;
}

/**
* 汎用ボタンコンポーネント
* @example
* <Button variant="primary" onClick={handleSubmit}>
*   送信する
* </Button>
*/
export const Button: React.FC<ButtonProps> = ({ 
 variant = 'primary',
 onClick,
 children 
}) => {
 return (
   <button 
     className={styles[variant]}
     onClick={onClick}
   >
     {children}
   </button>
 );
};
```

### エラーメッセージ

```typescript
// ユーザー向けのエラーメッセージは日本語で
const validateEmail = (email: string): string | null => {
 if (!email) {
   return 'メールアドレスを入力してください';
 }
 if (!email.includes('@')) {
   return '有効なメールアドレスを入力してください';
 }
 return null;
};

// 開発者向けのエラーは英語でも可（ただし日本語コメントを併記）
if (!context) {
 // Context が提供されていない場合のエラー
 throw new Error('useAppContext must be used within AppProvider');
}
```

## React/Next.js 開発規約

### Next.js App Router

```typescript
// app/page.tsx - Server Component がデフォルト
export default async function HomePage() {
 // サーバーサイドでデータ取得
 const data = await fetchData();
 
 return (
   <main>
     {/* クライアントコンポーネントに初期データを渡す */}
     <ClientComponent initialData={data} />
   </main>
 );
}

// components/ClientComponent.tsx
'use client';

export function ClientComponent({ initialData }) {
 const [state, setState] = useState(initialData);
 
 // インタラクティブな処理
 const handleUpdate = () => {
   // 状態更新ロジック
 };
 
 return (
   <div>
     {/* UIの実装 */}
   </div>
 );
}
```

### カスタムフック

```typescript
/**
* デバウンス処理を行うカスタムフック
* @param value - デバウンスする値
* @param delay - 遅延時間（ミリ秒）
* @returns デバウンスされた値
*/
export function useDebounce<T>(value: T, delay: number): T {
 const [debouncedValue, setDebouncedValue] = useState<T>(value);

 useEffect(() => {
   // 指定時間後に値を更新
   const handler = setTimeout(() => {
     setDebouncedValue(value);
   }, delay);

   // クリーンアップ
   return () => clearTimeout(handler);
 }, [value, delay]);

 return debouncedValue;
}
```

## 開発ワークフロー

### プルリクエスト

タイトルと説明は日本語で記述：

```markdown
## 概要
ユーザー認証機能を実装しました。

## 変更内容
- NextAuth.js を使用した認証システムの構築
- ログイン/ログアウト UI の実装
- 保護されたルートの設定

## テスト内容
- ユニットテスト：認証フローのテスト
- E2E テスト：ログインからダッシュボード表示までのフロー

## チェックリスト
- [ ] TypeScript の型エラーがない
- [ ] ESLint の警告がない
- [ ] テストがすべてパス
- [ ] ドキュメントを更新した
```

### Issue 管理

日本語で課題を記述：

```markdown
## 問題の概要
ページネーションで最後のページに移動できない

## 再現手順
1. 商品一覧ページを開く
2. 最後のページ番号をクリック
3. エラーが表示される

## 期待される動作
最後のページが正常に表示される

## 環境
- Next.js: 14.0.0
- React: 18.2.0
- ブラウザ: Chrome 120
```

## TDD サイクル

### 1. Red（失敗するテストを書く）

```typescript
// Button.test.tsx
describe('Button コンポーネント', () => {
 it('クリック時にハンドラーが呼ばれる', async () => {
   const handleClick = jest.fn();
   render(<Button onClick={handleClick}>テスト</Button>);
   
   const button = screen.getByRole('button');
   await userEvent.click(button);
   
   expect(handleClick).toHaveBeenCalledTimes(1);
 });
});
```

### 2. Green（最小限の実装）

```typescript
// Button.tsx
export const Button: React.FC<ButtonProps> = ({ onClick, children }) => {
 return <button onClick={onClick}>{children}</button>;
};
```

### 3. Refactor（改善）

```typescript
// Button.tsx - スタイルとアクセシビリティを追加
export const Button: React.FC<ButtonProps> = ({ 
 variant = 'primary',
 onClick,
 children,
 disabled = false,
 ariaLabel
}) => {
 return (
   <button 
     className={cn(styles.button, styles[variant])}
     onClick={onClick}
     disabled={disabled}
     aria-label={ariaLabel}
   >
     {children}
   </button>
 );
};
```

## TypeScript 設定

```json
{
 "compilerOptions": {
   "strict": true,
   "noImplicitAny": true,
   "strictNullChecks": true,
   "noUnusedLocals": true,
   "noUnusedParameters": true,
   "noImplicitReturns": true,
   "allowJs": false,
   "jsx": "preserve",
   "moduleResolution": "bundler",
   "paths": {
     "@/*": ["./src/*"],
     "@components/*": ["./src/components/*"],
     "@hooks/*": ["./src/hooks/*"],
     "@utils/*": ["./src/utils/*"]
   }
 }
}
```

## パフォーマンス最適化

### コンポーネント最適化

```typescript
// メモ化による再レンダリング防止
const ExpensiveComponent = memo(({ data }: Props) => {
 // 重い計算処理をメモ化
 const processedData = useMemo(() => {
   return heavyProcessing(data);
 }, [data]);
 
 // コールバック関数をメモ化
 const handleClick = useCallback(() => {
   // クリック処理
 }, [/* 依存配列 */]);
 
 return <div>{/* UI */}</div>;
});

// 動的インポートによるコード分割
const HeavyChart = dynamic(() => import('@/components/HeavyChart'), {
 loading: () => <p>グラフを読み込み中...</p>,
 ssr: false // クライアントサイドのみでレンダリング
});
```

## アクセシビリティ

```typescript
// フォームのアクセシビリティ
<form onSubmit={handleSubmit}>
 <label htmlFor="email">
   メールアドレス
   <span aria-label="必須" className="text-red-500">*</span>
 </label>
 <input
   id="email"
   type="email"
   required
   aria-invalid={!!errors.email}
   aria-describedby={errors.email ? "email-error" : undefined}
 />
 {errors.email && (
   <span id="email-error" role="alert" className="text-red-500">
     {errors.email}
   </span>
 )}
</form>
```

## 作業完了報告

### 完全完了時の合い言葉

すべての条件を満たした場合のみ：

```text
May the Force be with you.
```

**使用条件**：
- ✅ すべてのタスクが 100% 完了
- ✅ TODO リストが空
- ✅ TypeScript エラーゼロ
- ✅ ESLint エラー・警告ゼロ
- ✅ テストがすべてパス
- ✅ ビルドが成功
- ✅ ドキュメント（日本語）が更新済み

### 部分完了時の報告

```markdown
## 実行完了

### 変更内容
- [実装したコンポーネント/機能]
- [修正したバグ]

### 次のステップ
- [残っている実装タスク]
- [必要なレビュー項目]
```

## よくある実装パターン

### データフェッチング（SWR）

```typescript
// SWR を使用したデータ取得
import useSWR from 'swr';

const fetcher = (url: string) => fetch(url).then(res => res.json());

function UserProfile({ userId }: { userId: string }) {
 const { data, error, isLoading } = useSWR(
   `/api/users/${userId}`,
   fetcher,
   {
     // エラー時のリトライ設定
     errorRetryCount: 3,
     // リアルタイム更新の間隔
     refreshInterval: 30000, // 30秒
   }
 );
 
 if (isLoading) return <div>読み込み中...</div>;
 if (error) return <div>エラーが発生しました</div>;
 
 return <Profile user={data} />;
}
```

### フォーム処理（React Hook Form + Zod）

```typescript
// スキーマ定義（日本語エラーメッセージ）
const schema = z.object({
 email: z.string()
   .min(1, 'メールアドレスを入力してください')
   .email('有効なメールアドレスを入力してください'),
 password: z.string()
   .min(8, 'パスワードは8文字以上で入力してください')
   .regex(/[A-Z]/, '大文字を1文字以上含めてください')
   .regex(/[0-9]/, '数字を1文字以上含めてください'),
 confirmPassword: z.string()
}).refine((data) => data.password === data.confirmPassword, {
 message: 'パスワードが一致しません',
 path: ['confirmPassword'],
});

type FormData = z.infer<typeof schema>;

function SignUpForm() {
 const { 
   register, 
   handleSubmit, 
   formState: { errors, isSubmitting } 
 } = useForm<FormData>({
   resolver: zodResolver(schema)
 });
 
 const onSubmit = async (data: FormData) => {
   try {
     // API 呼び出し
     await createUser(data);
     toast.success('アカウントを作成しました');
   } catch (error) {
     toast.error('エラーが発生しました');
   }
 };
 
 return (
   <form onSubmit={handleSubmit(onSubmit)}>
     {/* フォームフィールド */}
   </form>
 );
}
```

## 制約事項

### Web 検索の制約

- **WebSearch ツールは使用禁止**
- **代替手段**：`gemini --prompt "WebSearch: <検索クエリ>"` を使用

### パッケージ選定

推奨ライブラリ：
- **状態管理**：Zustand
- **スタイリング**：Tailwind CSS
- **フォーム**：React Hook Form + Zod
- **テスト**：Vitest、React Testing Library、Playwright
- **UI コンポーネント**：shadcn/ui
- **データフェッチ**：TanStack Query
- **アニメーション**：Framer Motion
- **日付処理**：date-fns（日本語ロケール対応）

## 継続改善

- React/Next.js の新機能を随時学習・適用
- パフォーマンス指標（Core Web Vitals）の監視
- アクセシビリティスコアの向上
- コンポーネントライブラリの拡充
- 日本語ドキュメントの充実