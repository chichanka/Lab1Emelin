#include <windows.h>
#include <stdio.h>
#define TIMEOUT_LIMIT 10000
int main() {
STARTUPINFO si;
PROCESS_INFORMATION pi;
DWORD dwExitCode;
BOOL bResult;

ZeroMemory(&si, sizeof(si));
si.cb = sizeof(si);

ZeroMemory(&pi, sizeof(pi));

bResult = CreateProcess(
"notepad.exe",NULL,NULL,NULL,FALSE,0,NULL,NULL,&si,&pi                 
);

if (!bResult) {
printf("Помилка при створенні процесу. Код помилки: %d\n", GetLastError());
return 1;
}

printf("Процес створено, ID процесу: %d\n", pi.dwProcessId);

DWORD dwWaitResult = WaitForSingleObject(pi.hProcess, TIMEOUT_LIMIT);

if (dwWaitResult == WAIT_OBJECT_0) {
if (GetExitCodeProcess(pi.hProcess, &dwExitCode)) {
printf("Процес завершено з кодом: %d\n", dwExitCode);
} else {
printf("Не вдалося отримати код завершення процесу.\n");
}
} else if (dwWaitResult == WAIT_TIMEOUT) {
printf("Час виконання процесу перевищує ліміт. Завершуємо процес...\n");
TerminateProcess(pi.hProcess, 0);
printf("Процес було завершено примусово.\n");
} else {
printf("Помилка при очікуванні на процес. Код помилки: %d\n", GetLastError());
}

CloseHandle(pi.hProcess);
CloseHandle(pi.hThread);

return 0;
}
