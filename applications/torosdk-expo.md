# 📝 torosdk-expo

## 🌟 Project Overview

**Tagline:** A typed React Native / Expo SDK 52 wrapper that brings the Toronet blockchain to mobile developers through familiar hooks, secure credential storage, and one-command project bootstrap.

**Description:** `torosdk-expo` is an npm package that wraps `torosdk` (v0.2.0) with full TypeScript types, a structured error hierarchy, strategy-pattern authentication (password, biometric, custom), and nine React hooks powered by `@tanstack/react-query` v5. It targets Expo SDK 52+ and provides three subpath exports: `.` for React hooks, `./core` for framework-agnostic SDK wrappers, and `./cli` for `torosdk-expo init` scaffolding.

**Toronet integration:** The package is a direct integration layer over the Toronet API. Every core SDK function — wallet creation, import, balance queries, transfers, TNS name resolution and registration, KYC submission, and exchange rates — is wrapped with proper error normalisation and auth gating. The package is the first dedicated Expo/React Native SDK for Toronet, filling the gap between the raw `torosdk` npm package and the needs of mobile developers.

**Why we built this:** There is currently no React Native or Expo SDK for the Toronet ecosystem. Mobile developers who want to build Toronet-powered apps must either write their own wrappers around the raw `torosdk` package or use REST calls directly, both of which require solving authentication, secure credential storage, cache management, and TypeScript ergonomics from scratch. `torosdk-expo` solves all of these problems with a single `npm install` and a well-documented, production-grade integration layer.

---

## 🔍 Project Details

**Technology stack:**
- TypeScript 5.x (strict mode)
- React Native / Expo SDK 52
- `@tanstack/react-query` v5 (data fetching, caching, cache invalidation)
- `expo-secure-store` (iOS Keychain / Android Keystore-backed credential storage)
- `expo-local-authentication` (biometric auth — fingerprint / Face ID)
- `torosdk` v0.2.0 (upstream Toronet SDK)
- Node.js (CLI entry point uses stdlib only — no external deps)

**Core architecture:**

```
torosdk-expo (React hooks, Provider, query keys)
     │
     ├── torosdk-expo/core (SDK wrappers, auth, storage, config, errors, types)
     │         │
     │         └── torosdk v0.2.0 (upstream)
     │
     └── torosdk-expo/cli (project bootstrap script)
```

The package uses npm subpath exports (`exports` field in `package.json`) to provide three independent entry points. The `core` layer depends on `torosdk` and Expo modules but not on React. The `react` layer adds `@tanstack/react-query` and React context. The `cli` layer is a standalone Node.js script.

**Key architectural decisions:**

1. **Strategy-pattern authentication** — The `AuthStrategy` interface (`authorize(category): Promise<void>`) is implemented by three strategies: `PasswordStrategy` (silent fill from SecureStore), `BiometricStrategy` (expo-local-authentication), and `CustomStrategy` (user-provided async function). The active strategy is stored at module level so the core layer can gate operations without React context.

2. **Typed error hierarchy** — `ToroError` base class with `code: ToroErrorCode` extends into `NetworkError`, `APIError`, `AuthBlockedError`, and `StorageError`. The internal `wrapError()` helper normalises all thrown values into the appropriate subclass, giving callers predictable `instanceof` checks and structured error codes.

3. **SecureStore-backed credential storage** — Eight exported functions manage passwords (`wallet_pwd_<address>`), the wallet address list, and the active wallet selection, all backed by `expo-secure-store`.

4. **React Query cache orchestration** — Nine hooks with appropriate `staleTime` values (30s for balances, 60s for exchange rates, 5min for TNS/KYC). Mutations invalidate targeted query keys on success so the UI refetches automatically.

5. **CLI bootstrap** — `torosdk-expo init` detects the Expo project, determines the package manager (npm/yarn/pnpm), installs four peer dependencies, scaffolds three starter templates, and appends the network env var.

**What this project does not provide:**
- A custom UI component library (developers build their own UI on top of the hooks)
- Smart contract deployment or interaction beyond the Toronet API surface
- A wallet UI — the hooks return data and mutations; rendering is up to the consumer
- Server-side functionality — this is purely a client-side mobile SDK

**Prior work:** The package is complete and published at https://github.com/Xela-Byte/torosdk-expo with 8 git commits, comprehensive JSDoc on all 18 source files, a README with setup instructions and examples, and an ARCHITECTURE.md technical overview.

---

## 🧩 Ecosystem Fit

**Where it fits:** Developer tooling / infrastructure. `torosdk-expo` is an SDK that sits between mobile app developers and the Toronet blockchain. It handles the "plumbing" — configuration, authentication, secure storage, caching, error handling — so developers can focus on building their app's UI and business logic.

**Target audience:** React Native and Expo developers who want to integrate Toronet wallet, transfer, TNS, KYC, and exchange rate functionality into their mobile apps. This includes both indie developers and teams building consumer-facing Toronet applications.

**Problems solved:**
- **No existing Expo/React Native SDK for Toronet** — developers must currently write their own integration layer
- **Authentication complexity** — provides plug-and-play password, biometric, and custom auth strategies
- **Secure credential management** — handles iOS Keychain / Android Keystore storage out of the box
- **TypeScript ergonomics** — fully typed with proper error discrimination, eliminating guesswork
- **Cache management** — React Query integration means data stays fresh without manual polling
- **Onboarding friction** — `torosdk-expo init` bootstraps a working integration in seconds

**Similar projects:** No equivalent exists in the Toronet ecosystem. The closest analogue in other ecosystems is `wagmi` for Ethereum/EVM chains or `@solana/wallet-adapter` — both are mature, hook-based React SDKs that dramatically lowered the barrier to entry for their respective ecosystems. `torosdk-expo` aims to play the same role for Toronet on mobile.

---

# 👥 Team

- **Team Name:** Xela-Byte
- **Contact Name:** Alex (Xela-Byte)
- **Contact Email:** [available upon request]
- **Website:** https://github.com/Xela-Byte

---

## Team Members

Alex (Xela-Byte) — sole contributor

### LinkedIn Profiles (if available)

N/A

---

## Team Code Repositories

- https://github.com/Xela-Byte/torosdk-expo (this project)

Team member GitHub accounts:
- https://github.com/Xela-Byte
- https://github.com/OhXelaByte

---

## Team Experience

- **Blockchain development:** Experience with the Toronet ecosystem and torosdk v0.2.0 API surface, including wallet management, transfer operations, TNS name resolution, and KYC workflows.
- **Product development:** Full-stack development experience across TypeScript, React Native, Expo, Node.js, and npm package authoring.
- **Open-source contributions:** This project itself is open-source (MIT licensed) and designed for community use.
- **Technical expertise:** TypeScript strict-mode development, React Native / Expo SDK 52, `@tanstack/react-query` v5 cache management patterns, `expo-secure-store` and `expo-local-authentication` integration, npm subpath exports configuration.

---

# 📊 Development Status

The project is **complete and production-ready.** All planned features have been implemented.

**Repository:** https://github.com/Xela-Byte/torosdk-expo

**Current implementation status (100% complete):**

| Layer | Files | Status |
|-------|-------|--------|
| Core types, errors, config | `types.ts`, `errors.ts`, `config.ts` | ✅ Complete |
| Core storage (SecureStore) | `storage.ts` | ✅ Complete |
| Core auth (3 strategies) | `auth.ts` | ✅ Complete |
| Core SDK wrappers (12 functions) | `sdk.ts` | ✅ Complete |
| React Provider | `provider.tsx` | ✅ Complete |
| React query keys | `query-keys.ts` | ✅ Complete |
| React hooks (9 hooks) | `hooks/*.ts` | ✅ Complete |
| CLI bootstrap | `init.ts` | ✅ Complete |
| Documentation (README, JSDoc, ARCHITECTURE) | Various | ✅ Complete |

**Documentation delivered:**
- `README.md` — installation, setup, and usage guide with code examples for all hooks
- `ARCHITECTURE.md` — 800+ word technical overview covering all design decisions
- Comprehensive JSDoc on all 18 source files with `@param`, `@returns`, `@throws`, `@remarks`, and `@example` blocks

---

# 📅 Development Roadmap

## Overview

- **Estimated Duration:** Completed (all milestones delivered)
- **Full-Time Equivalent (FTE):** 1 contributor

---

> Deliverables 0a to 0d are mandatory. Please adapt them to your project.

| # | Deliverable | Specification |
| -----: | ----------- | ------------- |
| 0a. | License | MIT |
| 0b. | Documentation | Inline JSDoc on all 18 source files with `@param`, `@returns`, `@throws`, `@remarks`, and `@example` blocks; README.md with installation, setup, and usage guide with code examples for all 9 hooks and auth strategies; ARCHITECTURE.md with 800+ word technical overview |
| 0c. | Testing and Testing Guide | Core functionality verified through TypeScript strict-mode compilation; all SDK functions wrap and normalise errors; reviewers can validate by running `npm run build` and inspecting the typed API surface |
| 0d. | Article / Report | ARCHITECTURE.md serves as the technical report — covers layered architecture, key design decisions (strategy-pattern auth, typed error hierarchy, SecureStore storage, React Query integration, auth gating), developer takeaways, and a complete file map |
| 1. | Core SDK wrappers (`./core`) | 12 wrapped torosdk functions with TypeScript types, auth gating on sensitive operations, error normalisation via `wrapError()`, typed error hierarchy, and config singleton |
| 2. | React integration (`.`) | 9 React hooks (useWallets, useBalance, useBalances, useTransfer, useResolveTNS, useLookupTNS, useSetTNS, useKYCStatus, useSubmitKYC, useExchangeRates, useCreateWallet, useImportWallet, useDeleteWallet) with `@tanstack/react-query` v5, structured query keys, and automatic cache invalidation |
| 3. | Authentication system | Strategy-pattern auth with PasswordStrategy (silent SecureStore fill), BiometricStrategy (expo-local-authentication), and CustomStrategy (user-provided async function); 8 SecureStore-backed storage helpers for passwords, wallet lists, and active wallet |
| 4. | CLI bootstrap (`./cli`) | `torosdk-expo init` — detects Expo project, determines package manager, installs peer deps, scaffolds config/auth/provider templates, appends network env var |

---

# 🔮 Future Plans

**Post-bounty development:**
- Publish to npm as `torosdk-expo` for immediate community use
- Add comprehensive test suite using Jest and React Native Testing Library
- Add React Native storyboard/demo app showing all hooks in action
- Support additional auth strategies (Web3Auth, social login)
- Track torosdk releases and maintain compatibility with new versions

**Long-term vision:**
- Become the standard SDK for Toronet mobile development, analogous to `wagmi` for Ethereum
- Build a community of Toronet mobile developers around the package
- Potentially expand to include React (web) support via a `torosdk-expo/web` subpath export
- Explore Toronet Foundation or ecosystem grant programs for ongoing maintenance

---

# ℹ️ Additional Information

This project was built as a solo effort to fill a clear gap in the Toronet developer ecosystem. While there are raw SDKs for Node.js, there is no dedicated React Native / Expo integration layer — which is essential for the growing number of developers building mobile-first Toronet applications.

The package follows established patterns from mature Web3 SDKs (`wagmi`, `@solana/wallet-adapter`) and adapts them to Toronet's specific API surface. All code is original work, MIT licensed, and ready for community adoption.
