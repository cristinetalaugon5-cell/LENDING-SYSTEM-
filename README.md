<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Personal Lending Tracker</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet" />
  <style>
    body {
      font-family: 'Inter', sans-serif;
      background-color: #eef2ef;
      color: #1e293b;
    }
    .compact-table th,
    .compact-table td {
      padding: 5px 6px;
      font-size: 11px;
    }
    @keyframes fadeUp {
      from { opacity: 0; transform: translateY(8px); }
      to { opacity: 1; transform: translateY(0); }
    }
    .animate-fade-up {
      animation: fadeUp 0.18s ease-out;
    }
  </style>
</head>
<body class="min-h-screen antialiased pb-10">

  <!-- ======================================================================= -->
  <!-- SECURITY LOCK / PASSCODE SCREEN                                         -->
  <!-- ======================================================================= -->
  <div id="auth-lock-screen" class="fixed inset-0 z-[100] bg-[#18392b] flex items-center justify-center p-4">
    <div class="bg-white w-full max-w-sm rounded-2xl p-5 shadow-2xl space-y-4 animate-fade-up">
      <div class="text-center space-y-1">
        <div class="mx-auto w-11 h-11 rounded-2xl bg-[#eef2ef] text-[#18392b] flex items-center justify-center mb-2">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2.2" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" d="M16.5 10.5V6.75a4.5 4.5 0 10-9 0v3.75m-.75 11.25h10.5a2.25 2.25 0 002.25-2.25v-6.75a2.25 2.25 0 00-2.25-2.25H6.75a2.25 2.25 0 00-2.25 2.25v6.75a2.25 2.25 0 002.25 2.25z" />
          </svg>
        </div>
        <h2 id="auth-screen-title" class="text-base font-extrabold text-[#18392b]">Personal Lending Tracker</h2>
        <p id="auth-screen-subtitle" class="text-xs text-slate-500">Enter your passcode to unlock</p>
      </div>

      <!-- Unlock Existing Passcode Form -->
      <form id="auth-unlock-form" onsubmit="handleUnlockSubmit(event)" class="space-y-3">
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Passcode / PIN</label>
          <input
            type="password"
            id="auth-unlock-input"
            required
            autocomplete="current-password"
            placeholder="Enter your passcode"
            class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-sm font-semibold tracking-wider focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]"
          />
          <p id="auth-error-msg" class="hidden mt-1.5 text-xs font-semibold text-rose-600"></p>
        </div>
        <button
          type="submit"
          class="w-full bg-[#18392b] hover:bg-[#122c21] active:scale-[0.99] text-white font-bold text-sm py-2.5 rounded-xl shadow-xs transition"
        >
          Unlock Tracker
        </button>
        <div id="auth-forgot-link-wrap" class="text-center pt-0.5">
          <button
            type="button"
            onclick="openRecoveryMode()"
            class="text-[11px] font-semibold text-slate-500 hover:text-[#18392b] underline underline-offset-2 transition"
          >
            Forgot Passcode?
          </button>
        </div>
      </form>

      <!-- Forgot Passcode / Security Question Recovery Form -->
      <form id="auth-recovery-form" onsubmit="handleRecoverySubmit(event)" class="hidden space-y-3">
        <div class="bg-[#eef2ef] rounded-xl p-2.5">
          <p class="text-[10px] font-bold uppercase tracking-wider text-slate-500">Security Question</p>
          <p id="auth-recovery-question-text" class="text-xs font-bold text-[#18392b] mt-0.5"></p>
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Your Answer</label>
          <input
            type="text"
            id="auth-recovery-answer"
            required
            autocomplete="off"
            placeholder="Enter your saved answer"
            class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]"
          />
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">New Passcode (min. 4 characters)</label>
          <input
            type="password"
            id="auth-recovery-new-pass"
            required
            minlength="4"
            autocomplete="new-password"
            placeholder="Enter new passcode"
            class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm font-semibold focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]"
          />
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Confirm New Passcode</label>
          <input
            type="password"
            id="auth-recovery-confirm-pass"
            required
            minlength="4"
            autocomplete="new-password"
            placeholder="Re-enter new passcode"
            class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm font-semibold focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]"
          />
          <p id="auth-recovery-error" class="hidden mt-1.5 text-xs font-semibold text-rose-600"></p>
        </div>
        <div class="grid grid-cols-2 gap-2 pt-0.5">
          <button
            type="button"
            onclick="initAuthLockScreen()"
            class="w-full bg-slate-100 hover:bg-slate-200 text-slate-800 font-semibold text-xs py-2.5 rounded-xl transition"
          >
            Back to Login
          </button>
          <button
            type="submit"
            class="w-full bg-[#18392b] hover:bg-[#122c21] text-white font-bold text-xs py-2.5 rounded-xl transition"
          >
            Reset &amp; Unlock
          </button>
        </div>
      </form>

      <!-- First-Time Setup Passcode Form -->
      <form id="auth-setup-form" onsubmit="handleSetupPasscodeSubmit(event)" class="hidden space-y-2.5 max-h-[75vh] overflow-y-auto pr-0.5">
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Create Passcode (min. 4 characters)</label>
          <input
            type="password"
            id="auth-setup-pass"
            required
            minlength="4"
            autocomplete="new-password"
            placeholder="e.g., 1234 or secret key"
            class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm font-semibold focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]"
          />
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Confirm Passcode</label>
          <input
            type="password"
            id="auth-setup-confirm"
            required
            minlength="4"
            autocomplete="new-password"
            placeholder="Re-enter passcode"
            class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm font-semibold focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]"
          />
        </div>
        <div class="border-t border-slate-100 pt-2">
          <label class="block text-xs font-semibold text-slate-700 mb-1">Recovery Security Question *</label>
          <select
            id="auth-setup-question"
            required
            class="w-full border border-slate-300 rounded-xl px-3 py-2 text-xs font-medium bg-white focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]"
          >
            <option value="What is your mother's maiden name?">What is your mother's maiden name?</option>
            <option value="What was the name of your first pet?">What was the name of your first pet?</option>
            <option value="What city were you born in?">What city were you born in?</option>
            <option value="What is your favorite childhood nickname?">What is your favorite childhood nickname?</option>
            <option value="What was the name of your elementary school?">What was the name of your elementary school?</option>
          </select>
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Security Answer *</label>
          <input
            type="text"
            id="auth-setup-answer"
            required
            autocomplete="off"
            placeholder="Your secret recovery answer"
            class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]"
          />
          <p id="auth-setup-error" class="hidden mt-1.5 text-xs font-semibold text-rose-600"></p>
        </div>
        <button
          type="submit"
          class="w-full bg-[#18392b] hover:bg-[#122c21] active:scale-[0.99] text-white font-bold text-sm py-2.5 rounded-xl shadow-xs transition"
        >
          Set Passcode &amp; Enter
        </button>
      </form>
    </div>
  </div>

  <!-- ======================================================================= -->
  <!-- MAIN APP SHELL (UNLOCKED)                                               -->
  <!-- ======================================================================= -->
  <div id="app-shell" class="hidden w-full min-h-screen flex flex-col">

    <!-- 1. Dark Forest Green Top Banner Header -->
    <header class="bg-[#18392b] text-white px-3 py-2.5 shadow-sm flex flex-wrap items-center justify-between gap-2">
      <div class="min-w-0 flex items-center gap-2">
        <h1 class="text-[15px] sm:text-lg font-extrabold tracking-tight leading-snug">Personal Lending Tracker</h1>
      </div>

      <!-- View Switcher + Security Controls -->
      <div class="shrink-0 flex flex-wrap items-center gap-1.5">
        <nav aria-label="Page Navigation" class="flex items-center bg-black/25 p-0.5 rounded-xl border border-white/10">
          <button
            type="button"
            id="nav-loans-btn"
            onclick="switchPage('loans')"
            class="px-2.5 py-1 rounded-lg text-[11px] font-bold bg-white text-[#18392b] shadow-2xs transition"
          >
            Active
          </button>
          <button
            type="button"
            id="nav-paid-btn"
            onclick="switchPage('paid')"
            class="px-2.5 py-1 rounded-lg text-[11px] font-bold text-emerald-100 hover:text-white transition"
          >
            Paid
          </button>
          <button
            type="button"
            id="nav-audit-btn"
            onclick="switchPage('audit')"
            class="px-2.5 py-1 rounded-lg text-[11px] font-bold text-emerald-100 hover:text-white transition"
          >
            Audit Log
          </button>
        </nav>

        <button
          type="button"
          onclick="openChangePasscodeModal()"
          title="Change Passcode"
          aria-label="Change Passcode"
          class="p-1.5 rounded-xl bg-black/25 hover:bg-black/40 text-emerald-100 hover:text-white border border-white/10 transition"
        >
          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="2.2" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 5.25a3 3 0 013 3m3 0a6 6 0 01-7.029 5.912c-.563-.097-1.159.026-1.563.43L10.5 17.25H8.25v2.25H6v2.25H2.25v-2.818c0-.597.237-1.17.659-1.591l6.499-6.499c.404-.404.527-1 .43-1.563A6 6 0 1121.75 8.25z" />
          </svg>
        </button>

        <button
          type="button"
          onclick="lockApp()"
          title="Lock Tracker"
          aria-label="Lock Tracker"
          class="px-2 py-1 rounded-xl bg-rose-600/90 hover:bg-rose-600 text-white text-[11px] font-bold flex items-center gap-1 shadow-2xs transition"
        >
          <svg class="w-3 h-3" fill="none" stroke="currentColor" stroke-width="2.3" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" d="M16.5 10.5V6.75a4.5 4.5 0 10-9 0v3.75m-.75 11.25h10.5a2.25 2.25 0 002.25-2.25v-6.75a2.25 2.25 0 00-2.25-2.25H6.75a2.25 2.25 0 00-2.25 2.25v6.75a2.25 2.25 0 002.25 2.25z" />
          </svg>
          <span>Lock</span>
        </button>
      </div>
    </header>

    <!-- Main Container -->
    <main class="w-full max-w-xl mx-auto px-3 pt-3 space-y-3 flex-1">

      <!-- Portfolio Summary Grid -->
      <section aria-label="Portfolio Summary" class="grid grid-cols-2 gap-2">
        <div class="bg-white rounded-xl p-2.5 border border-slate-200/80 shadow-2xs">
          <p class="text-[10px] font-bold uppercase tracking-wider text-slate-500">Principal Lent</p>
          <p id="stat-principal-lent" class="text-base font-extrabold text-slate-900 mt-0.5">₱0.00</p>
        </div>
        <div class="bg-white rounded-xl p-2.5 border border-slate-200/80 shadow-2xs">
          <p class="text-[10px] font-bold uppercase tracking-wider text-slate-500">Expected Interest</p>
          <p id="stat-expected-interest" class="text-base font-extrabold text-slate-900 mt-0.5">₱0.00</p>
        </div>
        <div class="bg-white rounded-xl p-2.5 border border-slate-200/80 shadow-2xs">
          <p class="text-[10px] font-bold uppercase tracking-wider text-slate-500">Cash Collected</p>
          <p id="stat-cash-collected" class="text-base font-extrabold text-emerald-700 mt-0.5">₱0.00</p>
        </div>
        <div class="bg-white rounded-xl p-2.5 border border-slate-200/80 shadow-2xs">
          <p class="text-[10px] font-bold uppercase tracking-wider text-slate-500">Realized Interest</p>
          <p id="stat-realized-interest" class="text-base font-extrabold text-[#18392b] mt-0.5">₱0.00</p>
        </div>
        <div class="col-span-2 bg-white rounded-xl px-3 py-2 border border-slate-200/80 shadow-2xs flex items-center justify-between">
          <span class="text-[10px] font-bold uppercase tracking-wider text-slate-500">Outstanding Balance</span>
          <span id="stat-outstanding-balance" class="text-sm font-extrabold text-amber-700">₱0.00</span>
        </div>
      </section>

      <!-- Action Button Row -->
      <section aria-label="Quick Actions" class="grid grid-cols-3 gap-2">
        <button
          type="button"
          onclick="openAddModal()"
          class="bg-[#18392b] hover:bg-[#122c21] active:scale-[0.99] text-white font-bold text-xs py-2.5 px-2 rounded-xl shadow-2xs transition text-center"
        >
          + New Loan
        </button>
        <button
          type="button"
          onclick="exportData()"
          class="bg-white hover:bg-slate-50 active:scale-[0.99] text-slate-800 font-bold text-xs py-2.5 px-2 rounded-xl border border-slate-300/90 shadow-2xs transition text-center"
        >
          Export Data
        </button>
        <label
          class="bg-white hover:bg-slate-50 active:scale-[0.99] text-slate-800 font-bold text-xs py-2.5 px-2 rounded-xl border border-slate-300/90 shadow-2xs transition text-center cursor-pointer flex items-center justify-center"
        >
          <span>Restore Backup</span>
          <input type="file" accept=".json,application/json" onchange="handleRestoreFile(event)" class="hidden" />
        </label>
      </section>

      <!-- =================================================================== -->
      <!-- PAGE 1: ACTIVE LOANS VIEW                                           -->
      <!-- =================================================================== -->
      <section id="page-loans-view" class="space-y-2.5">
        <div class="flex items-center justify-between px-0.5">
          <h2 class="text-xs font-extrabold uppercase tracking-wider text-slate-600">Active Borrowers</h2>
          <span id="active-count-label" class="text-[11px] font-semibold text-slate-500">0 active</span>
        </div>
        <div id="borrowers-list" class="space-y-2.5"></div>
      </section>

      <!-- =================================================================== -->
      <!-- PAGE 2: PAID LOANS VIEW (AUTO-COLLAPSED)                            -->
      <!-- =================================================================== -->
      <section id="page-paid-view" class="hidden space-y-2.5">
        <div class="flex items-center justify-between px-0.5">
          <h2 class="text-xs font-extrabold uppercase tracking-wider text-slate-600">Completed Loans</h2>
          <span id="paid-count-label" class="text-[11px] font-semibold text-slate-500">0 completed</span>
        </div>
        <div id="paid-borrowers-list" class="space-y-2"></div>
      </section>

      <!-- =================================================================== -->
      <!-- PAGE 3: AUDIT LOG VIEW                                              -->
      <!-- =================================================================== -->
      <section id="page-audit-view" class="hidden space-y-2.5">
        <div class="bg-white rounded-2xl border border-slate-200/90 p-3.5 shadow-2xs space-y-2.5">
          <div class="flex items-center justify-between border-b border-slate-100 pb-2">
            <div>
              <h2 class="text-xs font-extrabold uppercase tracking-wider text-[#18392b]">Activity &amp; Audit Log</h2>
              <p id="audit-count-label" class="text-[10px] text-slate-400">0 entries</p>
            </div>
            <button
              type="button"
              id="clear-audit-btn"
              onclick="handleClearAuditLog()"
              class="text-[11px] font-bold px-2.5 py-1 rounded-lg border border-rose-200 text-rose-700 hover:bg-rose-50 transition"
            >
              Clear Log
            </button>
          </div>
          <div id="audit-log-list" class="max-h-[60vh] overflow-y-auto pr-1"></div>
        </div>
      </section>

    </main>
  </div>

  <!-- Toast Notification Banner -->
  <div id="toast-box" class="fixed bottom-4 left-1/2 -translate-x-1/2 z-[110] hidden bg-[#18392b] text-white text-xs font-bold px-4 py-2.5 rounded-full shadow-lg"></div>

  <!-- ======================================================================= -->
  <!-- MODAL A: ADD NEW LOAN                                                   -->
  <!-- ======================================================================= -->
  <div id="add-modal" class="fixed inset-0 z-50 hidden bg-slate-900/50 backdrop-blur-xs flex items-end sm:items-center justify-center p-0 sm:p-4">
    <div class="bg-white w-full max-w-md rounded-t-2xl sm:rounded-2xl max-h-[92vh] flex flex-col shadow-xl animate-fade-up overflow-hidden">
      <div class="px-4 py-3.5 bg-[#18392b] text-white flex items-center justify-between">
        <h2 class="text-base font-bold">Add New Loan</h2>
        <button type="button" onclick="closeAddModal()" class="p-1.5 rounded-lg text-emerald-100 hover:text-white hover:bg-white/10">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/></svg>
        </button>
      </div>

      <form onsubmit="handleSaveLoan(event)" class="p-4 space-y-3 overflow-y-auto">
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Borrower Name *</label>
          <input type="text" id="input-borrower" required placeholder="e.g., Andrea" class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
        </div>

        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Payment Method / Notes (Optional)</label>
          <input type="text" id="input-notes" placeholder="e.g., GCash 0917..., ATM, or Payday" class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
        </div>

        <div class="grid grid-cols-2 gap-2.5">
          <div>
            <label class="block text-xs font-semibold text-slate-700 mb-1">Principal (₱) *</label>
            <input type="number" id="input-principal" required min="1" step="0.01" oninput="updateAddPreview()" placeholder="2000" class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm font-semibold focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
          </div>
          <div>
            <label class="block text-xs font-semibold text-slate-700 mb-1">Rate % / Term *</label>
            <input type="number" id="input-rate" required min="0" step="0.01" value="5" oninput="updateAddPreview()" class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm font-semibold focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
          </div>
        </div>

        <div class="grid grid-cols-2 gap-2.5">
          <div>
            <label class="block text-xs font-semibold text-slate-700 mb-1">Schedule</label>
            <select id="input-frequency" class="w-full border border-slate-300 rounded-xl px-3 py-2 text-sm bg-white focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]">
              <option value="semi-monthly">Semi-Monthly (15d)</option>
              <option value="monthly">Monthly (30d)</option>
            </select>
          </div>
          <div>
            <label class="block text-xs font-semibold text-slate-700 mb-1">No. of Terms *</label>
            <input type="number" id="input-terms" required min="1" max="60" value="2" oninput="updateAddPreview()" class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm font-semibold focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
          </div>
        </div>

        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Date Borrowed</label>
          <input type="date" id="input-borrowed-date" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
        </div>

        <div class="bg-[#eef2ef] rounded-xl p-3 flex items-center justify-between text-xs">
          <div>
            <span class="text-slate-500 block">Total Due</span>
            <span id="preview-total-due" class="font-extrabold text-sm text-[#18392b]">₱0.00</span>
          </div>
          <div class="text-right">
            <span class="text-slate-500 block">Installment</span>
            <span id="preview-per-term" class="font-bold text-slate-800">₱0.00 / term</span>
          </div>
        </div>

        <div class="grid grid-cols-2 gap-2.5 pt-1">
          <button type="button" onclick="closeAddModal()" class="w-full bg-slate-100 hover:bg-slate-200 text-slate-800 font-semibold text-sm py-2.5 rounded-xl transition">
            Cancel
          </button>
          <button type="submit" class="w-full bg-[#18392b] hover:bg-[#122c21] text-white font-bold text-sm py-2.5 rounded-xl transition">
            Save Loan
          </button>
        </div>
      </form>
    </div>
  </div>

  <!-- ======================================================================= -->
  <!-- MODAL B: RECORD PAYMENT & LATE FEE                                      -->
  <!-- ======================================================================= -->
  <div id="pay-modal" class="fixed inset-0 z-50 hidden bg-slate-900/50 backdrop-blur-xs flex items-end sm:items-center justify-center p-0 sm:p-4">
    <div class="bg-white w-full max-w-md rounded-t-2xl sm:rounded-2xl max-h-[92vh] flex flex-col shadow-xl animate-fade-up overflow-hidden">
      <div class="px-4 py-3.5 bg-[#18392b] text-white flex items-center justify-between">
        <div>
          <h2 class="text-base font-bold">Record Payment</h2>
          <p id="pay-modal-subtitle" class="text-[11px] text-emerald-100"></p>
        </div>
        <button type="button" onclick="closePayModal()" class="p-1.5 rounded-lg text-emerald-100 hover:text-white hover:bg-white/10">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/></svg>
        </button>
      </div>

      <form onsubmit="handleConfirmPayment(event)" class="p-4 space-y-3 overflow-y-auto">
        <input type="hidden" id="pay-loan-id" />
        <input type="hidden" id="pay-installment-index" />

        <div class="bg-[#eef2ef] rounded-xl p-3 flex items-center justify-between">
          <span class="text-xs font-semibold text-slate-600">Remaining Balance on Term</span>
          <span id="pay-remaining-display" class="text-base font-extrabold text-[#18392b]">₱0.00</span>
        </div>

        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Amount Paid Now (₱)</label>
          <input type="number" id="pay-amount-input" required min="0.01" step="0.01" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-base font-extrabold text-slate-900 focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
        </div>

        <div class="border-t border-slate-100 pt-2.5">
          <label class="block text-[11px] font-semibold text-slate-600 mb-1">Optional: Add Late / Extension Fee (₱)</label>
          <div class="flex gap-2">
            <input type="number" id="pay-fee-input" min="0" step="0.01" value="0" class="flex-1 border border-slate-300 rounded-xl px-3 py-1.5 text-xs font-semibold focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
            <button type="button" onclick="handleAddLateFee()" class="px-3 py-1.5 rounded-xl bg-amber-100 hover:bg-amber-200 text-amber-900 font-bold text-xs transition">
              + Add Fee
            </button>
          </div>
        </div>

        <div class="grid grid-cols-2 gap-2.5 pt-1">
          <button type="button" onclick="closePayModal()" class="w-full bg-slate-100 hover:bg-slate-200 text-slate-800 font-semibold text-sm py-2.5 rounded-xl transition">
            Cancel
          </button>
          <button type="submit" class="w-full bg-[#18392b] hover:bg-[#122c21] text-white font-bold text-sm py-2.5 rounded-xl transition">
            Confirm Payment
          </button>
        </div>
      </form>
    </div>
  </div>

  <!-- ======================================================================= -->
  <!-- MODAL C: DELETE BORROWER CONFIRMATION                                   -->
  <!-- ======================================================================= -->
  <div id="delete-modal" class="fixed inset-0 z-50 hidden bg-slate-900/50 backdrop-blur-xs flex items-center justify-center p-4">
    <div class="bg-white w-full max-w-sm rounded-2xl p-5 shadow-xl space-y-3 animate-fade-up">
      <h3 class="text-base font-extrabold text-slate-900">Remove Borrower?</h3>
      <p id="delete-modal-text" class="text-xs text-slate-600 leading-relaxed">Remove this loan record permanently?</p>
      <div class="grid grid-cols-2 gap-2.5 pt-1">
        <button type="button" onclick="closeDeleteModal()" class="w-full bg-slate-100 hover:bg-slate-200 text-slate-800 font-semibold text-sm py-2 rounded-xl transition">
          Cancel
        </button>
        <button type="button" onclick="confirmDeleteLoan()" class="w-full bg-rose-600 hover:bg-rose-700 text-white font-bold text-sm py-2 rounded-xl transition">
          Remove
        </button>
      </div>
    </div>
  </div>

  <!-- ======================================================================= -->
  <!-- MODAL D: EDIT BORROWER DETAILS                                          -->
  <!-- ======================================================================= -->
  <div id="edit-borrower-modal" class="fixed inset-0 z-50 hidden bg-slate-900/50 backdrop-blur-xs flex items-end sm:items-center justify-center p-0 sm:p-4">
    <div class="bg-white w-full max-w-md rounded-t-2xl sm:rounded-2xl max-h-[92vh] flex flex-col shadow-xl animate-fade-up overflow-hidden">
      <div class="px-4 py-3.5 bg-[#18392b] text-white flex items-center justify-between">
        <h2 class="text-base font-bold">Edit Borrower Info</h2>
        <button type="button" onclick="closeEditBorrowerModal()" class="p-1.5 rounded-lg text-emerald-100 hover:text-white hover:bg-white/10">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/></svg>
        </button>
      </div>
      <form onsubmit="handleSaveEditBorrower(event)" class="p-4 space-y-3 overflow-y-auto">
        <input type="hidden" id="edit-loan-id" />
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Borrower Name</label>
          <input type="text" id="edit-borrower-name" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Payment Method / Notes (e.g., Pays via ATM)</label>
          <input type="text" id="edit-borrower-notes" placeholder="e.g., Pays via ATM" class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Date Borrowed</label>
          <input type="date" id="edit-borrowed-date" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
        </div>
        <div class="grid grid-cols-2 gap-2.5 pt-1">
          <button type="button" onclick="closeEditBorrowerModal()" class="w-full bg-slate-100 hover:bg-slate-200 text-slate-800 font-semibold text-sm py-2.5 rounded-xl transition">
            Cancel
          </button>
          <button type="submit" class="w-full bg-[#18392b] hover:bg-[#122c21] text-white font-bold text-sm py-2.5 rounded-xl transition">
            Save Changes
          </button>
        </div>
      </form>
    </div>
  </div>

  <!-- ======================================================================= -->
  <!-- MODAL E: CHANGE PASSCODE                                                -->
  <!-- ======================================================================= -->
  <div id="passcode-modal" class="fixed inset-0 z-50 hidden bg-slate-900/50 backdrop-blur-xs flex items-end sm:items-center justify-center p-0 sm:p-4">
    <div class="bg-white w-full max-w-md rounded-t-2xl sm:rounded-2xl max-h-[92vh] flex flex-col shadow-xl animate-fade-up overflow-hidden">
      <div class="px-4 py-3.5 bg-[#18392b] text-white flex items-center justify-between">
        <h2 class="text-base font-bold">Change Passcode</h2>
        <button type="button" onclick="closeChangePasscodeModal()" class="p-1.5 rounded-lg text-emerald-100 hover:text-white hover:bg-white/10">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/></svg>
        </button>
      </div>
      <form onsubmit="handleChangePasscode(event)" class="p-4 space-y-3 overflow-y-auto">
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Current Passcode</label>
          <input type="password" id="change-pass-current" required autocomplete="current-password" class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">New Passcode (min. 4 characters)</label>
          <input type="password" id="change-pass-new" required minlength="4" autocomplete="new-password" class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Confirm New Passcode</label>
          <input type="password" id="change-pass-confirm" required minlength="4" autocomplete="new-password" class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
        </div>
        <div class="border-t border-slate-100 pt-2">
          <label class="block text-xs font-semibold text-slate-700 mb-1">Security Question</label>
          <select id="change-pass-question" class="w-full border border-slate-300 rounded-xl px-3 py-2 text-xs font-medium bg-white focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]">
            <option value="What is your mother's maiden name?">What is your mother's maiden name?</option>
            <option value="What was the name of your first pet?">What was the name of your first pet?</option>
            <option value="What city were you born in?">What city were you born in?</option>
            <option value="What is your favorite childhood nickname?">What is your favorite childhood nickname?</option>
            <option value="What was the name of your elementary school?">What was the name of your elementary school?</option>
          </select>
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Update Security Answer (Leave blank to keep existing)</label>
          <input type="text" id="change-pass-answer" autocomplete="off" placeholder="Enter new answer or leave blank" class="w-full border border-slate-300 rounded-xl px-3.5 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#18392b]/25 focus:border-[#18392b]" />
          <p id="change-pass-error" class="hidden mt-1.5 text-xs font-semibold text-rose-600"></p>
        </div>
        <div class="grid grid-cols-2 gap-2.5 pt-1">
          <button type="button" onclick="closeChangePasscodeModal()" class="w-full bg-slate-100 hover:bg-slate-200 text-slate-800 font-semibold text-sm py-2.5 rounded-xl transition">
            Cancel
          </button>
          <button type="submit" class="w-full bg-[#18392b] hover:bg-[#122c21] text-white font-bold text-sm py-2.5 rounded-xl transition">
            Update Passcode
          </button>
        </div>
      </form>
    </div>
  </div>

  <script>
    const STORAGE_KEY = 'personal_lending_tracker_v1';
    const AUDIT_STORAGE_KEY = 'personal_lending_tracker_audit_v1';
    const AUTH_STORAGE_KEY = 'personal_lending_tracker_auth_v1';

    let loans = [];
    let auditLogs = [];
    let activePage = 'loans';
    let expandedPaidIds = new Set();
    let pendingDeleteId = null;

    function roundMoney(num) {
      return Math.round((Number(num) + Number.EPSILON) * 100) / 100;
    }

    function formatCurrency(amount) {
      const safe = Number.isFinite(Number(amount)) ? Number(amount) : 0;
      return '₱' + safe.toLocaleString('en-PH', {
        minimumFractionDigits: 2,
        maximumFractionDigits: 2
      });
    }

    function escapeHtml(str) {
      if (!str) return '';
      return String(str)
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#39;');
    }

    function getTodayISO() {
      const now = new Date();
      const y = now.getFullYear();
      const m = String(now.getMonth() + 1).padStart(2, '0');
      const d = String(now.getDate()).padStart(2, '0');
      return `${y}-${m}-${d}`;
    }

    function addDaysISO(isoDateStr, daysToAdd) {
      const parts = isoDateStr.split('-').map(Number);
      const dt = new Date(parts[0], parts[1] - 1, parts[2]);
      dt.setDate(dt.getDate() + daysToAdd);
      const y = dt.getFullYear();
      const m = String(dt.getMonth() + 1).padStart(2, '0');
      const d = String(dt.getDate()).padStart(2, '0');
      return `${y}-${m}-${d}`;
    }

    function formatDateShort(isoDateStr) {
      if (!isoDateStr) return '—';
      const parts = isoDateStr.split('-').map(Number);
      if (parts.length !== 3) return isoDateStr;
      const dt = new Date(parts[0], parts[1] - 1, parts[2]);
      return dt.toLocaleDateString('en-US', { month: 'short', day: 'numeric', year: 'numeric' });
    }

    function showToast(msg) {
      const box = document.getElementById('toast-box');
      if (!box) return;
      box.textContent = msg;
      box.classList.remove('hidden');
      clearTimeout(box._timer);
      box._timer = setTimeout(() => box.classList.add('hidden'), 2400);
    }

    async function hashPasscode(text) {
      const normalized = String(text).trim().toLowerCase();
      const encoder = new TextEncoder();
      const data = encoder.encode(normalized);
      const hashBuffer = await crypto.subtle.digest('SHA-256', data);
      const hashArray = Array.from(new Uint8Array(hashBuffer));
      return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
    }

    function loadAuthConfig() {
      try {
        const raw = localStorage.getItem(AUTH_STORAGE_KEY);
        if (!raw) return null;
        const parsed = JSON.parse(raw);
        if (parsed && typeof parsed.hash === 'string') return parsed;
        return null;
      } catch (e) {
        return null;
      }
    }

    function saveAuthConfig(cfg) {
      localStorage.setItem(AUTH_STORAGE_KEY, JSON.stringify(cfg));
    }

    function initAuthLockScreen() {
      const lockScreen = document.getElementById('auth-lock-screen');
      const appShell = document.getElementById('app-shell');
      const unlockForm = document.getElementById('auth-unlock-form');
      const recoveryForm = document.getElementById('auth-recovery-form');
      const setupForm = document.getElementById('auth-setup-form');
      const titleEl = document.getElementById('auth-screen-title');
      const subtitleEl = document.getElementById('auth-screen-subtitle');
      const forgotWrap = document.getElementById('auth-forgot-link-wrap');
      const errMsg = document.getElementById('auth-error-msg');
      const setupErr = document.getElementById('auth-setup-error');

      if (lockScreen) lockScreen.classList.remove('hidden');
      if (appShell) appShell.classList.add('hidden');
      if (errMsg) errMsg.classList.add('hidden');
      if (setupErr) setupErr.classList.add('hidden');
      if (recoveryForm) recoveryForm.classList.add('hidden');

      const cfg = loadAuthConfig();
      if (!cfg) {
        if (titleEl) titleEl.textContent = 'Set Up Passcode';
        if (subtitleEl) subtitleEl.textContent = 'Create a passcode & recovery question for privacy';
        if (unlockForm) unlockForm.classList.add('hidden');
        if (setupForm) setupForm.classList.remove('hidden');
        setTimeout(() => document.getElementById('auth-setup-pass')?.focus(), 50);
      } else {
        if (titleEl) titleEl.textContent = 'Personal Lending Tracker';
        if (subtitleEl) subtitleEl.textContent = 'Enter your passcode to unlock';
        if (setupForm) setupForm.classList.add('hidden');
        if (unlockForm) unlockForm.classList.remove('hidden');
        if (forgotWrap) {
          if (cfg.question && cfg.answerHash) forgotWrap.classList.remove('hidden');
          else forgotWrap.classList.add('hidden');
        }
        const input = document.getElementById('auth-unlock-input');
        if (input) {
          input.value = '';
          setTimeout(() => input.focus(), 50);
        }
      }
    }

    function openRecoveryMode() {
      const cfg = loadAuthConfig();
      if (!cfg || !cfg.question || !cfg.answerHash) {
        showToast('No security question set on this device');
        return;
      }
      document.getElementById('auth-screen-title').textContent = 'Recover Passcode';
      document.getElementById('auth-screen-subtitle').textContent = 'Answer your security question to reset';
      document.getElementById('auth-unlock-form').classList.add('hidden');
      document.getElementById('auth-setup-form').classList.add('hidden');
      document.getElementById('auth-recovery-form').classList.remove('hidden');
      document.getElementById('auth-recovery-question-text').textContent = cfg.question;
      document.getElementById('auth-recovery-answer').value = '';
      document.getElementById('auth-recovery-new-pass').value = '';
      document.getElementById('auth-recovery-confirm-pass').value = '';
      document.getElementById('auth-recovery-error').classList.add('hidden');
      setTimeout(() => document.getElementById('auth-recovery-answer')?.focus(), 50);
    }

    async function handleRecoverySubmit(event) {
      event.preventDefault();
      const errEl = document.getElementById('auth-recovery-error');
      const answerInput = document.getElementById('auth-recovery-answer').value.trim();
      const newPass = document.getElementById('auth-recovery-new-pass').value;
      const confirmPass = document.getElementById('auth-recovery-confirm-pass').value;
      const cfg = loadAuthConfig();

      if (!cfg || !cfg.answerHash) {
        errEl.textContent = 'Recovery is not configured.';
        errEl.classList.remove('hidden');
        return;
      }

      const enteredAnswerHash = await hashPasscode(answerInput);
      if (enteredAnswerHash !== cfg.answerHash) {
        errEl.textContent = 'Incorrect security answer.';
        errEl.classList.remove('hidden');
        return;
      }

      if (newPass.length < 4) {
        errEl.textContent = 'New passcode must be at least 4 characters.';
        errEl.classList.remove('hidden');
        return;
      }

      if (newPass !== confirmPass) {
        errEl.textContent = 'New passcodes do not match.';
        errEl.classList.remove('hidden');
        return;
      }

      const newHash = await hashPasscode(newPass);
      saveAuthConfig({
        hash: newHash,
        question: cfg.question,
        answerHash: cfg.answerHash
      });

      errEl.classList.add('hidden');
      unlockApp();
      showToast('Passcode reset & unlocked');
    }

    async function handleSetupPasscodeSubmit(event) {
      event.preventDefault();
      const pass = document.getElementById('auth-setup-pass').value;
      const confirm = document.getElementById('auth-setup-confirm').value;
      const question = document.getElementById('auth-setup-question').value;
      const answer = document.getElementById('auth-setup-answer').value.trim();
      const errEl = document.getElementById('auth-setup-error');

      if (pass.length < 4) {
        errEl.textContent = 'Passcode must be at least 4 characters.';
        errEl.classList.remove('hidden');
        return;
      }
      if (pass !== confirm) {
        errEl.textContent = 'Passcodes do not match.';
        errEl.classList.remove('hidden');
        return;
      }
      if (!answer) {
        errEl.textContent = 'Please enter a recovery answer.';
        errEl.classList.remove('hidden');
        return;
      }

      const hash = await hashPasscode(pass);
      const answerHash = await hashPasscode(answer);
      saveAuthConfig({ hash, question, answerHash });
      errEl.classList.add('hidden');
      unlockApp();
      showToast('Passcode & security question saved');
    }

    async function handleUnlockSubmit(event) {
      event.preventDefault();
      const input = document.getElementById('auth-unlock-input');
      const errEl = document.getElementById('auth-error-msg');
      const cfg = loadAuthConfig();
      if (!cfg) {
        initAuthLockScreen();
        return;
      }

      const enteredHash = await hashPasscode(input.value);
      if (enteredHash === cfg.hash) {
        errEl.classList.add('hidden');
        input.value = '';
        unlockApp();
      } else {
        errEl.textContent = 'Incorrect passcode. Please try again.';
        errEl.classList.remove('hidden');
        input.select();
      }
    }

    function unlockApp() {
      document.getElementById('auth-lock-screen')?.classList.add('hidden');
      document.getElementById('app-shell')?.classList.remove('hidden');
      renderApp();
    }

    function lockApp() {
      initAuthLockScreen();
      showToast('Tracker locked');
    }

    function openChangePasscodeModal() {
      const cfg = loadAuthConfig();
      document.getElementById('change-pass-current').value = '';
      document.getElementById('change-pass-new').value = '';
      document.getElementById('change-pass-confirm').value = '';
      document.getElementById('change-pass-answer').value = '';
      if (cfg && cfg.question) {
        document.getElementById('change-pass-question').value = cfg.question;
      }
      document.getElementById('change-pass-error').classList.add('hidden');
      document.getElementById('passcode-modal').classList.remove('hidden');
      setTimeout(() => document.getElementById('change-pass-current')?.focus(), 50);
    }

    function closeChangePasscodeModal() {
      document.getElementById('passcode-modal').classList.add('hidden');
    }

    async function handleChangePasscode(event) {
      event.preventDefault();
      const currentVal = document.getElementById('change-pass-current').value;
      const newVal = document.getElementById('change-pass-new').value;
      const confirmVal = document.getElementById('change-pass-confirm').value;
      const questionVal = document.getElementById('change-pass-question').value;
      const answerVal = document.getElementById('change-pass-answer').value.trim();
      const errEl = document.getElementById('change-pass-error');
      const cfg = loadAuthConfig();

      if (!cfg) {
        closeChangePasscodeModal();
        initAuthLockScreen();
        return;
      }

      const currentHash = await hashPasscode(currentVal);
      if (currentHash !== cfg.hash) {
        errEl.textContent = 'Current passcode is incorrect.';
        errEl.classList.remove('hidden');
        return;
      }

      if (newVal.length < 4) {
        errEl.textContent = 'New passcode must be at least 4 characters.';
        errEl.classList.remove('hidden');
        return;
      }

      if (newVal !== confirmVal) {
        errEl.textContent = 'New passcodes do not match.';
        errEl.classList.remove('hidden');
        return;
      }

      const newHash = await hashPasscode(newVal);
      const nextAnswerHash = answerVal ? await hashPasscode(answerVal) : cfg.answerHash;

      saveAuthConfig({
        hash: newHash,
        question: questionVal,
        answerHash: nextAnswerHash
      });

      errEl.classList.add('hidden');
      closeChangePasscodeModal();
      recordAudit('PASSCODE_CHANGED', 'Updated tracker passcode & recovery settings');
      showToast('Passcode updated');
    }

    function switchPage(pageName) {
      activePage = pageName;
      const loansView = document.getElementById('page-loans-view');
      const paidView = document.getElementById('page-paid-view');
      const auditView = document.getElementById('page-audit-view');
      const loansBtn = document.getElementById('nav-loans-btn');
      const paidBtn = document.getElementById('nav-paid-btn');
      const auditBtn = document.getElementById('nav-audit-btn');

      const inactiveClass = 'px-2.5 py-1 rounded-lg text-[11px] font-bold text-emerald-100 hover:text-white transition';
      const activeClass = 'px-2.5 py-1 rounded-lg text-[11px] font-bold bg-white text-[#18392b] shadow-2xs transition';

      loansBtn.className = inactiveClass;
      paidBtn.className = inactiveClass;
      auditBtn.className = inactiveClass;

      loansView.classList.add('hidden');
      paidView.classList.add('hidden');
      auditView.classList.add('hidden');

      if (pageName === 'paid') {
        paidBtn.className = activeClass;
        paidView.classList.remove('hidden');
      } else if (pageName === 'audit') {
        auditBtn.className = activeClass;
        auditView.classList.remove('hidden');
      } else {
        loansBtn.className = activeClass;
        loansView.classList.remove('hidden');
      }
      renderApp();
    }

    function recordAudit(actionType, details) {
      const entry = {
        id: 'aud_' + Date.now() + '_' + Math.random().toString(36).slice(2, 6),
        timestamp: new Date().toISOString(),
        action: actionType,
        details: details
      };
      auditLogs.unshift(entry);
      if (auditLogs.length > 250) {
        auditLogs = auditLogs.slice(0, 250);
      }
      saveAuditLogs();
    }

    function saveAuditLogs() {
      localStorage.setItem(AUDIT_STORAGE_KEY, JSON.stringify(auditLogs));
    }

    function loadAuditLogs() {
      try {
        const raw = localStorage.getItem(AUDIT_STORAGE_KEY);
        if (!raw) return [];
        const parsed = JSON.parse(raw);
        return Array.isArray(parsed) ? parsed : [];
      } catch (e) {
        return [];
      }
    }

    function handleClearAuditLog() {
      if (auditLogs.length === 0) return;
      auditLogs = [];
      saveAuditLogs();
      renderAuditLog();
      showToast('Audit log cleared');
    }

    function formatAuditTime(isoStr) {
      const dt = new Date(isoStr);
      if (Number.isNaN(dt.getTime())) return isoStr;
      return dt.toLocaleString('en-US', {
        month: 'short',
        day: 'numeric',
        hour: '2-digit',
        minute: '2-digit'
      });
    }

    function renderAuditLog() {
      const listEl = document.getElementById('audit-log-list');
      const countEl = document.getElementById('audit-count-label');
      const clearBtn = document.getElementById('clear-audit-btn');
      if (!listEl || !countEl) return;

      countEl.textContent = `${auditLogs.length} ${auditLogs.length === 1 ? 'entry' : 'entries'}`;
      if (clearBtn) {
        clearBtn.disabled = auditLogs.length === 0;
        clearBtn.classList.toggle('opacity-40', auditLogs.length === 0);
        clearBtn.classList.toggle('cursor-not-allowed', auditLogs.length === 0);
      }

      if (auditLogs.length === 0) {
        listEl.innerHTML = `<p class="text-xs text-slate-400 italic py-4 text-center">No activity recorded yet.</p>`;
        return;
      }

      listEl.innerHTML = `
        <div class="divide-y divide-slate-100">
          ${auditLogs.map(item => `
            <div class="py-2 flex items-start justify-between gap-2 text-[11px]">
              <div class="min-w-0">
                <span class="inline-block font-bold text-[10px] px-1.5 py-0.5 rounded bg-[#eef2ef] text-[#18392b] mr-1.5">${escapeHtml(item.action)}</span>
                <span class="text-slate-700 break-words">${escapeHtml(item.details)}</span>
              </div>
              <span class="shrink-0 text-[10px] text-slate-400 whitespace-nowrap">${escapeHtml(formatAuditTime(item.timestamp))}</span>
            </div>
          `).join('')}
        </div>
      `;
    }

    function buildInstallments(principal, ratePercent, numTerms, frequency, borrowedDate) {
      const totalInterest = roundMoney(principal * (ratePercent / 100) * numTerms);
      const totalDue = roundMoney(principal + totalInterest);
      const baseInstallment = roundMoney(totalDue / numTerms);
      const daysPerTerm = frequency === 'monthly' ? 30 : 15;

      const principalPerTerm = roundMoney(principal / numTerms);
      const interestPerTerm = roundMoney(totalInterest / numTerms);

      const installments = [];
      let runningTotal = 0;
      let runningPrincipal = 0;
      let runningInterest = 0;

      for (let i = 0; i < numTerms; i++) {
        let amt, pPart, iPart;
        if (i < numTerms - 1) {
          amt = baseInstallment;
          pPart = principalPerTerm;
          iPart = interestPerTerm;
          runningTotal = roundMoney(runningTotal + amt);
          runningPrincipal = roundMoney(runningPrincipal + pPart);
          runningInterest = roundMoney(runningInterest + iPart);
        } else {
          amt = roundMoney(totalDue - runningTotal);
          pPart = roundMoney(principal - runningPrincipal);
          iPart = roundMoney(totalInterest - runningInterest);
        }

        installments.push({
          term: i + 1,
          dueDate: addDaysISO(borrowedDate, daysPerTerm * (i + 1)),
          amountDue: amt,
          principalPortion: pPart,
          interestPortion: iPart,
          amountPaid: 0
        });
      }

      return { totalInterest, totalDue, installments };
    }

    function getLoanMetrics(loan) {
      const installments = loan.installments || [];
      const totalDue = roundMoney(installments.reduce((sum, item) => sum + Number(item.amountDue || 0), 0));
      const totalPaid = roundMoney(installments.reduce((sum, item) => sum + Number(item.amountPaid || 0), 0));
      const remainingBalance = roundMoney(Math.max(0, totalDue - totalPaid));
      const isPaid = installments.length > 0 && remainingBalance <= 0.009;

      const totalScheduledInterest = roundMoney(installments.reduce((sum, item) => sum + Number(item.interestPortion || 0), 0));
      let realizedInterest = 0;
      installments.forEach(item => {
        const due = Number(item.amountDue || 0);
        const paid = Number(item.amountPaid || 0);
        const iPortion = Number(item.interestPortion || 0);
        if (due > 0 && paid > 0) {
          const ratio = Math.min(1, paid / due);
          realizedInterest = roundMoney(realizedInterest + iPortion * ratio);
        }
      });

      return {
        principal: roundMoney(loan.principal || 0),
        totalScheduledInterest,
        totalDue,
        totalPaid,
        realizedInterest,
        remainingBalance,
        isPaid
      };
    }

    function calculatePortfolioStats() {
      let principalLent = 0;
      let expectedInterest = 0;
      let cashCollected = 0;
      let realizedInterest = 0;
      let outstandingBalance = 0;

      loans.forEach(loan => {
        const m = getLoanMetrics(loan);
        principalLent = roundMoney(principalLent + m.principal);
        expectedInterest = roundMoney(expectedInterest + m.totalScheduledInterest);
        cashCollected = roundMoney(cashCollected + m.totalPaid);
        realizedInterest = roundMoney(realizedInterest + m.realizedInterest);
        outstandingBalance = roundMoney(outstandingBalance + m.remainingBalance);
      });

      return {
        principalLent,
        expectedInterest,
        cashCollected,
        realizedInterest,
        outstandingBalance
      };
    }

    function saveLoans() {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(loans));
    }

    function loadLoans() {
      try {
        const raw = localStorage.getItem(STORAGE_KEY);
        if (!raw) return [];
        const parsed = JSON.parse(raw);
        return Array.isArray(parsed) ? parsed : [];
      } catch (e) {
        return [];
      }
    }

    function openAddModal(prefill = null) {
      document.getElementById('input-borrower').value = prefill?.borrowerName || '';
      document.getElementById('input-notes').value = prefill?.notes || '';
      document.getElementById('input-principal').value = prefill?.principal || '';
      document.getElementById('input-rate').value = prefill?.ratePercent ?? 5;
      document.getElementById('input-frequency').value = prefill?.frequency || 'semi-monthly';
      document.getElementById('input-terms').value = prefill?.numTerms ?? 2;
      document.getElementById('input-borrowed-date').value = getTodayISO();
      updateAddPreview();
      document.getElementById('add-modal').classList.remove('hidden');
      setTimeout(() => document.getElementById('input-borrower')?.focus(), 50);
    }

    function closeAddModal() {
      document.getElementById('add-modal').classList.add('hidden');
    }

    function reLoanBorrower(loanId) {
      const source = loans.find(l => l.id === loanId);
      if (!source) return;
      openAddModal({
        borrowerName: source.borrowerName,
        notes: source.notes || '',
        principal: source.principal,
        ratePercent: source.ratePercent,
        frequency: source.frequency,
        numTerms: source.numTerms
      });
    }

    function updateAddPreview() {
      const p = parseFloat(document.getElementById('input-principal').value) || 0;
      const r = parseFloat(document.getElementById('input-rate').value) || 0;
      const t = parseInt(document.getElementById('input-terms').value, 10) || 1;
      const interest = roundMoney(p * (r / 100) * t);
      const total = roundMoney(p + interest);
      const perTerm = t > 0 ? roundMoney(total / t) : 0;

      document.getElementById('preview-total-due').textContent = formatCurrency(total);
      document.getElementById('preview-per-term').textContent = `${formatCurrency(perTerm)} / term`;
    }

    function handleSaveLoan(event) {
      event.preventDefault();
      const borrowerName = document.getElementById('input-borrower').value.trim();
      const notes = document.getElementById('input-notes').value.trim();
      const principal = roundMoney(parseFloat(document.getElementById('input-principal').value));
      const ratePercent = roundMoney(parseFloat(document.getElementById('input-rate').value));
      const frequency = document.getElementById('input-frequency').value;
      const numTerms = parseInt(document.getElementById('input-terms').value, 10);
      const borrowedDate = document.getElementById('input-borrowed-date').value || getTodayISO();

      if (!borrowerName || principal <= 0 || numTerms < 1) return;

      const built = buildInstallments(principal, ratePercent, numTerms, frequency, borrowedDate);
      const newLoan = {
        id: 'loan_' + Date.now() + '_' + Math.random().toString(36).slice(2, 6),
        borrowerName,
        notes,
        principal,
        ratePercent,
        frequency,
        numTerms,
        borrowedDate,
        totalInterest: built.totalInterest,
        totalDue: built.totalDue,
        installments: built.installments,
        createdAt: new Date().toISOString()
      };

      loans.unshift(newLoan);
      saveLoans();
      recordAudit('LOAN_CREATED', `${borrowerName} borrowed ${formatCurrency(principal)} (${ratePercent}% × ${numTerms} terms)`);
      closeAddModal();
      if (activePage !== 'loans') {
        switchPage('loans');
      } else {
        renderApp();
      }
      showToast(`Loan added for ${borrowerName}`);
    }

    function openEditBorrowerModal(loanId) {
      const loan = loans.find(l => l.id === loanId);
      if (!loan) return;
      document.getElementById('edit-loan-id').value = loan.id;
      document.getElementById('edit-borrower-name').value = loan.borrowerName;
      document.getElementById('edit-borrower-notes').value = loan.notes || '';
      document.getElementById('edit-borrowed-date').value = loan.borrowedDate || getTodayISO();
      document.getElementById('edit-borrower-modal').classList.remove('hidden');
    }

    function closeEditBorrowerModal() {
      document.getElementById('edit-borrower-modal').classList.add('hidden');
    }

    function handleSaveEditBorrower(event) {
      event.preventDefault();
      const loanId = document.getElementById('edit-loan-id').value;
      const loan = loans.find(l => l.id === loanId);
      if (!loan) return;

      const nextName = document.getElementById('edit-borrower-name').value.trim();
      const nextNotes = document.getElementById('edit-borrower-notes').value.trim();
      const nextDate = document.getElementById('edit-borrowed-date').value;

      if (!nextName) return;

      loan.borrowerName = nextName;
      loan.notes = nextNotes;
      if (nextDate) loan.borrowedDate = nextDate;

      saveLoans();
      recordAudit('BORROWER_EDITED', `Updated info for ${nextName}${nextNotes ? ` (${nextNotes})` : ''}`);
      closeEditBorrowerModal();
      renderApp();
      showToast('Borrower details updated');
    }

    function openPayModal(loanId, installmentIndex) {
      const loan = loans.find(l => l.id === loanId);
      if (!loan) return;
      const item = loan.installments[installmentIndex];
      if (!item) return;

      const remaining = roundMoney(Math.max(0, Number(item.amountDue) - Number(item.amountPaid)));
      document.getElementById('pay-loan-id').value = loanId;
      document.getElementById('pay-installment-index').value = installmentIndex;
      document.getElementById('pay-modal-subtitle').textContent = `${loan.borrowerName} • Term ${item.term} (Due ${formatDateShort(item.dueDate)})`;
      document.getElementById('pay-remaining-display').textContent = formatCurrency(remaining);
      document.getElementById('pay-amount-input').value = remaining > 0 ? remaining.toFixed(2) : '';
      document.getElementById('pay-fee-input').value = '0';
      document.getElementById('pay-modal').classList.remove('hidden');
      setTimeout(() => document.getElementById('pay-amount-input')?.focus(), 50);
    }

    function closePayModal() {
      document.getElementById('pay-modal').classList.add('hidden');
    }

    function handleAddLateFee() {
      const loanId = document.getElementById('pay-loan-id').value;
      const idx = parseInt(document.getElementById('pay-installment-index').value, 10);
      const fee = roundMoney(parseFloat(document.getElementById('pay-fee-input').value) || 0);
      if (fee <= 0) {
        showToast('Enter a fee amount greater than 0');
        return;
      }

      const loan = loans.find(l => l.id === loanId);
      if (!loan || !loan.installments[idx]) return;

      const item = loan.installments[idx];
      item.amountDue = roundMoney(Number(item.amountDue) + fee);
      item.interestPortion = roundMoney(Number(item.interestPortion || 0) + fee);
      loan.totalInterest = roundMoney(Number(loan.totalInterest || 0) + fee);
      loan.totalDue = roundMoney(Number(loan.totalDue || 0) + fee);

      saveLoans();
      recordAudit('FEE_ADDED', `Added ${formatCurrency(fee)} fee to ${loan.borrowerName} Term ${item.term}`);

      const newRemaining = roundMoney(Math.max(0, Number(item.amountDue) - Number(item.amountPaid)));
      document.getElementById('pay-remaining-display').textContent = formatCurrency(newRemaining);
      document.getElementById('pay-amount-input').value = newRemaining.toFixed(2);
      document.getElementById('pay-fee-input').value = '0';
      renderApp();
      showToast(`Added ${formatCurrency(fee)} fee`);
    }

    function handleConfirmPayment(event) {
      event.preventDefault();
      const loanId = document.getElementById('pay-loan-id').value;
      const idx = parseInt(document.getElementById('pay-installment-index').value, 10);
      const paidAmt = roundMoney(parseFloat(document.getElementById('pay-amount-input').value));

      if (!Number.isFinite(paidAmt) || paidAmt <= 0) return;

      const loan = loans.find(l => l.id === loanId);
      if (!loan || !loan.installments[idx]) return;

      const item = loan.installments[idx];
      const maxAllowable = roundMoney(Math.max(0, Number(item.amountDue) - Number(item.amountPaid)));
      const applied = Math.min(paidAmt, maxAllowable > 0 ? maxAllowable : paidAmt);

      item.amountPaid = roundMoney(Number(item.amountPaid) + applied);

      saveLoans();
      recordAudit('PAYMENT_RECORDED', `${loan.borrowerName} paid ${formatCurrency(applied)} on Term ${item.term}`);
      closePayModal();
      renderApp();
      showToast(`Recorded ${formatCurrency(applied)} payment`);
    }

    function updateInstallmentDate(loanId, idx, newDate) {
      if (!newDate) return;
      const loan = loans.find(l => l.id === loanId);
      if (!loan || !loan.installments[idx]) return;
      loan.installments[idx].dueDate = newDate;
      saveLoans();
      recordAudit('DUE_DATE_UPDATED', `${loan.borrowerName} Term ${loan.installments[idx].term} due date set to ${formatDateShort(newDate)}`);
      renderApp();
      showToast('Due date updated');
    }

    function openDeleteModal(loanId) {
      const loan = loans.find(l => l.id === loanId);
      if (!loan) return;
      pendingDeleteId = loanId;
      document.getElementById('delete-modal-text').textContent =
        `Remove ${loan.borrowerName}'s loan (${formatCurrency(loan.principal)}) permanently?`;
      document.getElementById('delete-modal').classList.remove('hidden');
    }

    function closeDeleteModal() {
      pendingDeleteId = null;
      document.getElementById('delete-modal').classList.add('hidden');
    }

    function confirmDeleteLoan() {
      if (!pendingDeleteId) return;
      const target = loans.find(l => l.id === pendingDeleteId);
      loans = loans.filter(l => l.id !== pendingDeleteId);
      saveLoans();
      if (target) {
        recordAudit('LOAN_DELETED', `Removed loan for ${target.borrowerName} (${formatCurrency(target.principal)})`);
      }
      closeDeleteModal();
      renderApp();
      showToast('Loan removed');
    }

    function togglePaidCardExpand(loanId) {
      if (expandedPaidIds.has(loanId)) {
        expandedPaidIds.delete(loanId);
      } else {
        expandedPaidIds.add(loanId);
      }
      renderApp();
    }

    function renderLoanCard(loan, isCompletedTab) {
      const m = getLoanMetrics(loan);
      const isExpanded = expandedPaidIds.has(loan.id);

      const statusBadge = m.isPaid
        ? `<span class="inline-flex items-center px-2 py-0.5 rounded-full text-[10px] font-bold bg-emerald-100 text-emerald-800">Paid</span>`
        : `<span class="inline-flex items-center px-2 py-0.5 rounded-full text-[10px] font-bold bg-amber-100 text-amber-800">In Progress</span>`;

      const reLoanBtn = m.isPaid
        ? `<button type="button" onclick="reLoanBorrower('${loan.id}')" class="px-2 py-0.5 rounded-lg bg-[#18392b] hover:bg-[#122c21] text-white text-[10px] font-bold transition">Re-Loan</button>`
        : '';

      if (isCompletedTab && !isExpanded) {
        return `
          <article class="bg-white rounded-2xl border border-slate-200/90 px-3.5 py-2.5 shadow-2xs flex items-center justify-between gap-2">
            <button type="button" onclick="togglePaidCardExpand('${loan.id}')" class="min-w-0 flex-1 flex items-center gap-2 text-left group">
              <svg class="w-3.5 h-3.5 text-slate-400 group-hover:text-[#18392b] shrink-0 transition" fill="none" stroke="currentColor" stroke-width="2.3" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7" />
              </svg>
              <span class="font-bold text-sm text-slate-900 truncate">${escapeHtml(loan.borrowerName)}</span>
              ${loan.notes ? `<span class="text-[11px] text-slate-400 truncate hidden sm:inline">• ${escapeHtml(loan.notes)}</span>` : ''}
            </button>
            <div class="shrink-0 flex items-center gap-2">
              <span class="text-xs font-extrabold text-emerald-700">${formatCurrency(m.totalPaid)} Paid</span>
              ${reLoanBtn}
              <button type="button" onclick="openDeleteModal('${loan.id}')" title="Delete" class="p-1 text-slate-400 hover:text-rose-600 transition">
                <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"/></svg>
              </button>
            </div>
          </article>
        `;
      }

      const rowsHtml = (loan.installments || []).map((item, idx) => {
        const due = Number(item.amountDue || 0);
        const paid = Number(item.amountPaid || 0);
        const rem = roundMoney(Math.max(0, due - paid));
        const termDone = rem <= 0.009;

        return `
          <tr class="border-t border-slate-100 ${termDone ? 'bg-emerald-50/40' : ''}">
            <td class="font-bold text-slate-700">#${item.term}</td>
            <td>
              <input
                type="date"
                value="${escapeHtml(item.dueDate || '')}"
                onchange="updateInstallmentDate('${loan.id}', ${idx}, this.value)"
                class="bg-transparent border border-transparent hover:border-slate-300 focus:border-[#18392b] rounded px-1 py-0.5 text-[11px] text-slate-700 font-medium focus:outline-none"
              />
            </td>
            <td class="font-semibold text-slate-800">${formatCurrency(due)}</td>
            <td class="font-semibold text-emerald-700">${formatCurrency(paid)}</td>
            <td class="font-bold ${termDone ? 'text-emerald-700' : 'text-amber-700'}">${formatCurrency(rem)}</td>
            <td class="text-right">
              ${termDone
                ? `<span class="inline-block px-2 py-0.5 rounded-md text-[10px] font-bold bg-emerald-100 text-emerald-800">Paid</span>`
                : `<button type="button" onclick="openPayModal('${loan.id}', ${idx})" class="px-2 py-1 rounded-md text-[10px] font-bold bg-[#18392b] hover:bg-[#122c21] text-white transition">Pay</button>`
              }
            </td>
          </tr>
        `;
      }).join('');

      return `
        <article class="bg-white rounded-2xl border border-slate-200/90 p-3.5 shadow-2xs space-y-2.5">
          <div class="flex items-start justify-between gap-2">
            <div class="min-w-0">
              <div class="flex items-center gap-1.5 flex-wrap">
                ${isCompletedTab ? `
                  <button type="button" onclick="togglePaidCardExpand('${loan.id}')" class="p-0.5 text-slate-500 hover:text-[#18392b]" title="Collapse">
                    <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="2.3" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
                    </svg>
                  </button>
                ` : ''}
                <h3 class="font-extrabold text-sm text-slate-900 truncate">${escapeHtml(loan.borrowerName)}</h3>
                ${statusBadge}
                ${reLoanBtn}
              </div>
              ${loan.notes ? `<p class="text-[11px] font-medium text-emerald-800 bg-emerald-50/80 inline-block px-2 py-0.5 rounded-md mt-1">${escapeHtml(loan.notes)}</p>` : ''}
              <p class="text-[11px] text-slate-500 mt-0.5">
                Borrowed ${escapeHtml(formatDateShort(loan.borrowedDate))} • ${escapeHtml(String(loan.ratePercent))}% / term
              </p>
            </div>

            <div class="shrink-0 flex items-center gap-1">
              <button type="button" onclick="openEditBorrowerModal('${loan.id}')" title="Edit Borrower" class="p-1.5 rounded-lg text-slate-400 hover:text-[#18392b] hover:bg-slate-100 transition">
                <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M16.862 4.487l1.687-1.688a1.875 1.875 0 112.652 2.652L10.582 16.07a4.5 4.5 0 01-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 011.13-1.897l8.932-8.931z"/></svg>
              </button>
              <button type="button" onclick="openDeleteModal('${loan.id}')" title="Delete Loan" class="p-1.5 rounded-lg text-slate-400 hover:text-rose-600 hover:bg-rose-50 transition">
                <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"/></svg>
              </button>
            </div>
          </div>

          <div class="grid grid-cols-3 gap-1.5 bg-[#eef2ef] rounded-xl p-2 text-center">
            <div>
              <span class="block text-[9px] font-bold uppercase text-slate-500">Principal</span>
              <span class="text-xs font-extrabold text-slate-800">${formatCurrency(m.principal)}</span>
            </div>
            <div>
              <span class="block text-[9px] font-bold uppercase text-slate-500">Paid</span>
              <span class="text-xs font-extrabold text-emerald-700">${formatCurrency(m.totalPaid)}</span>
            </div>
            <div>
              <span class="block text-[9px] font-bold uppercase text-slate-500">Balance</span>
              <span class="text-xs font-extrabold text-amber-700">${formatCurrency(m.remainingBalance)}</span>
            </div>
          </div>

          <div class="pt-0.5">
            <p class="text-[10px] font-extrabold uppercase tracking-wider text-slate-500 mb-1">
              Installments (${loan.numTerms || (loan.installments || []).length})
            </p>
            <div class="overflow-x-auto border border-slate-100 rounded-xl">
              <table class="w-full compact-table text-left">
                <thead class="bg-slate-50 text-[10px] font-bold uppercase text-slate-500">
                  <tr>
                    <th>Term</th>
                    <th>Due Date</th>
                    <th>Due</th>
                    <th>Paid</th>
                    <th>Left</th>
                    <th class="text-right">Action</th>
                  </tr>
                </thead>
                <tbody>${rowsHtml}</tbody>
              </table>
            </div>
          </div>
        </article>
      `;
    }

    function renderApp() {
      const stats = calculatePortfolioStats();
      document.getElementById('stat-principal-lent').textContent = formatCurrency(stats.principalLent);
      document.getElementById('stat-expected-interest').textContent = formatCurrency(stats.expectedInterest);
      document.getElementById('stat-cash-collected').textContent = formatCurrency(stats.cashCollected);
      document.getElementById('stat-realized-interest').textContent = formatCurrency(stats.realizedInterest);
      document.getElementById('stat-outstanding-balance').textContent = formatCurrency(stats.outstandingBalance);

      const activeLoans = loans.filter(l => !getLoanMetrics(l).isPaid);
      const paidLoans = loans.filter(l => getLoanMetrics(l).isPaid);

      document.getElementById('active-count-label').textContent = `${activeLoans.length} active`;
      document.getElementById('paid-count-label').textContent = `${paidLoans.length} completed`;

      const activeListEl = document.getElementById('borrowers-list');
      if (activeLoans.length === 0) {
        activeListEl.innerHTML = `
          <div class="bg-white rounded-2xl border border-slate-200/90 p-6 text-center space-y-1">
            <p class="text-sm font-bold text-slate-700">No active loans</p>
            <p class="text-xs text-slate-400">Tap "+ New Loan" above to add a borrower.</p>
          </div>
        `;
      } else {
        activeListEl.innerHTML = activeLoans.map(l => renderLoanCard(l, false)).join('');
      }

      const paidListEl = document.getElementById('paid-borrowers-list');
      if (paidLoans.length === 0) {
        paidListEl.innerHTML = `
          <div class="bg-white rounded-2xl border border-slate-200/90 p-6 text-center">
            <p class="text-xs text-slate-400">Completed loans will appear here automatically once paid in full.</p>
          </div>
        `;
      } else {
        paidListEl.innerHTML = paidLoans.map(l => renderLoanCard(l, true)).join('');
      }

      renderAuditLog();
    }

    function exportData() {
      const payload = {
        exportedAt: new Date().toISOString(),
        loans,
        auditLogs
      };
      const blob = new Blob([JSON.stringify(payload, null, 2)], { type: 'application/json' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = `lending-tracker-backup-${getTodayISO()}.json`;
      a.click();
      URL.revokeObjectURL(url);
      recordAudit('DATA_EXPORTED', `Exported ${loans.length} loans backup`);
      showToast('Backup exported');
    }

    function handleRestoreFile(event) {
      const file = event.target.files?.[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = function (e) {
        try {
          const parsed = JSON.parse(e.target.result);
          if (Array.isArray(parsed)) {
            loans = parsed;
          } else if (parsed && Array.isArray(parsed.loans)) {
            loans = parsed.loans;
            if (Array.isArray(parsed.auditLogs)) {
              auditLogs = parsed.auditLogs;
              saveAuditLogs();
            }
          } else {
            showToast('Invalid backup file');
            return;
          }
          saveLoans();
          recordAudit('DATA_RESTORED', `Restored ${loans.length} loans from backup`);
          renderApp();
          showToast('Backup restored');
        } catch (err) {
          showToast('Could not read backup file');
        }
      };
      reader.readAsText(file);
      event.target.value = '';
    }

    loans = loadLoans();
    auditLogs = loadAuditLogs();
    initAuthLockScreen();
  </script>
</body>
</html>
