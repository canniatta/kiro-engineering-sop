# Prompts: Daily Workflows & Automation

> [!NOTE]
> **Source of Truth**
>
> - Master library Section 6: #[[file:docs/03-prompt-library.md]] (Section "Daily Workflows & Automation Prompts")
> - Workflow harian: #[[file:docs/18-workflow-daily-weekly-monthly.md]]
> - GitHub Flow: #[[file:docs/15-workflow-github-flow.md]]

---

## 1. Daily Standup Summary Generator

**Kapan digunakan:** Membuat ringkasan standup profesional dari git log harian — menghemat waktu pagi hari sebelum daily meeting.

```text
Here is my Git commit log for today:
```text
[Paste Git Commits]
```
Summarize this commit log into a professional Daily Standup update in Bahasa Indonesia using this structure:
1. *What I did yesterday*: (Summary of achievements)
2. *What I'm doing today*: (Next actions)
3. *Blockers*: (Any potential delays or dependencies)
```

> [!TIP]
> - Dapatkan git log dengan: `git log --oneline --since="yesterday" --author="$(git config user.name)"`
> - Tambahkan context tambahan yang tidak ter-cover di commit (meeting, review, investigation)
> - Hasil bisa langsung di-paste ke Slack/Teams channel standup

---

## 2. Automated PR Description Generator

**Kapan digunakan:** Mengisi template Pull Request secara otomatis dari code diff — memastikan deskripsi PR lengkap dan informatif.

```text
Here is the git diff for my current feature branch:
```diff
[Paste Git Diff Here]
```
Act as a Lead Engineer. Write a comprehensive Pull Request description based on this diff:
1. **Summary of Changes**: Bullet points of main changes.
2. **Type of Change**: (Feature, Bugfix, Refactoring, performance, etc.)
3. **Technical Impact**: Any database migrations, config changes, or breaking API changes.
4. **How Has This Been Tested**: Describe unit test, integration test, or manual validation.
```

> [!TIP]
> - Dapatkan diff dengan: `git diff main...HEAD` atau `git diff --staged`
> - Untuk diff besar, fokuskan pada file-file utama yang berubah (exclude auto-generated files)
> - Tambahkan screenshot atau recording jika perubahan melibatkan UI
> - Review PR description yang dihasilkan — pastikan tidak ada sensitive info yang ter-include

> [!IMPORTANT]
> PR description adalah dokumentasi untuk reviewer dan future reference. Pastikan section "Technical Impact" mencakup breaking changes yang perlu diperhatikan tim lain.
